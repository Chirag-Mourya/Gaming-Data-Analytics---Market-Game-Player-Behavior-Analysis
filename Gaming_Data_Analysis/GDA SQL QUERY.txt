-- ============================================================
--   GAME DATA ANALYSIS — SQL QUESTION BANK (15+ Questions)
--   Schema: game_data, genre_table, platform_table,
--           store_table, tag_table
--   Dialect: PostgreSQL
-- ============================================================

-- SCHEMA REFERENCE
-- game_data      : game_id, game_name, publisher, release_year,
--                  user_rating, metacritic, popularity_score,
--                  avg_playtime_hours, engagement_score,
--                  ratings_count, platform_count, is_multiplayer
-- genre_table    : game_id, genre
-- platform_table : game_id, platform
-- store_table    : game_id, store
-- tag_table      : game_id, tag
-- ============================================================


SELECT * FROM game_data;
ALTER TABLE game_data
ALTER COLUMN game_id TYPE INT
USING game_id::integer;

SELECT * FROM genre_table;
ALTER TABLE genre_table
ALTER COLUMN game_id TYPE INT;

SELECT * FROM platform_table;
ALTER TABLE platform_table
ALTER COLUMN game_id TYPE INT;

SELECT * FROM store_table;
ALTER TABLE store_table
ALTER COLUMN game_id TYPE INT;


SELECT * FROM tag_table;
ALTER TABLE tag_table
ALTER COLUMN game_id TYPE INT;


-- ============================================================
-- Q1. What Makes a Game Commercially Successful?
--     Compare top-10% popular games vs the rest across
--     multiple KPIs to identify success drivers.
-- ============================================================

WITH popularity_threshold AS (
							    SELECT PERCENTILE_CONT(0.90)
								WITHIN GROUP (ORDER BY popularity_score) AS cutoff
							    FROM game_data )

SELECT
    CASE
        WHEN popularity_score >= (SELECT cutoff FROM popularity_threshold)
            THEN 'Top 10% — Successful'
        ELSE 'Rest of Market'
    END AS game_category,
    COUNT(*)AS total_games,
    ROUND(AVG(user_rating)::numeric, 2)AS avg_user_rating,
    ROUND(AVG(metacritic)::numeric, 2) AS avg_metacritic,
    ROUND(AVG(platform_count)::numeric, 2)AS avg_platform_count,
    ROUND(AVG(avg_playtime_hours)::numeric, 2)AS avg_playtime_hours,
    ROUND(AVG(engagement_score)::numeric, 2)AS avg_engagement_score,
    ROUND(AVG(ratings_count)::numeric, 2)AS avg_ratings_count
FROM game_data
GROUP BY game_category
ORDER BY avg_engagement_score DESC;


-- =============================================================================
-- Q2. Which 5 Niche Genres Punch Above Their Weight?
--     Find genres with few games but above-average popularity and user rating.
-- =============================================================================

SELECT gt.genre, COUNT(DISTINCT gd.game_id) AS total_games,
			    ROUND(AVG(gd.popularity_score)::numeric, 2) AS avg_popularity_score,
			    ROUND(AVG(gd.user_rating)::numeric, 2) AS avg_user_rating
FROM game_data AS gd

JOIN genre_table AS gt
ON gd.game_id = gt.game_id

GROUP BY gt.genre
HAVING AVG(gd.popularity_score) > (SELECT AVG(popularity_score) FROM game_data)
    	AND
	   AVG(gd.user_rating) > (SELECT AVG(user_rating) FROM game_data)

ORDER BY total_games ASC
LIMIT 5;


-- =====================================================================================================================
-- Q3. Which Genres Generate the Highest Player Commitment?
--     Rank genres by avg playtime and engagement (min 20 games to remove noise) — guides long-term content investment.
-- =====================================================================================================================

SELECT
    gt.genre,
    COUNT(DISTINCT gd.game_id) AS total_games,
    ROUND(AVG(gd.avg_playtime_hours)::numeric, 2) AS avg_playtime_hours,
    ROUND(AVG(gd.engagement_score)::numeric, 2) AS avg_engagement_score
FROM game_data AS gd

JOIN genre_table AS gt
ON gd.game_id = gt.game_id

GROUP BY gt.genre
HAVING COUNT(DISTINCT gd.game_id) >= 20
ORDER BY avg_playtime_hours DESC
LIMIT(5);


-- ============================================================
-- Q4. Which Games Are Hidden Gems?
--     High user rating + high playtime but low popularity score
--     → under-marketed games worth promoting.
-- ============================================================

SELECT
    title AS game_name,
    publishers,
    release_year,
    user_rating,
    avg_playtime_hours,
    popularity_score
FROM game_data

WHERE
    user_rating > (SELECT PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY user_rating) FROM game_data)
    AND
    avg_playtime_hours > (SELECT PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY avg_playtime_hours) FROM game_data)
    AND
    popularity_score < (SELECT PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY popularity_score)  FROM game_data)
	
ORDER BY user_rating DESC,
		 avg_playtime_hours DESC
LIMIT 10;


-- ========================================================================================================================
-- Q5. Which Games Have Retention Problems?
--     High popularity (players try it) but low playtime and low engagement ie. games failing to keep players hooked.
-- ========================================================================================================================

SELECT
    gd.title AS game_name,
    gd.publishers,
    gd.popularity_score,
    gd.avg_playtime_hours,
    gd.engagement_score,
    ROUND((gd.engagement_score / NULLIF(gd.popularity_score, 0))::numeric, 4)
        AS retention_ratio
FROM game_data gd
WHERE
    gd.popularity_score   > (SELECT AVG(popularity_score)   FROM game_data)
    AND
    gd.avg_playtime_hours < (SELECT AVG(avg_playtime_hours) FROM game_data)
    AND
    gd.engagement_score   < (SELECT AVG(engagement_score)   FROM game_data)
ORDER BY retention_ratio ASC
LIMIT 10;


-- ============================================================================================
-- Q6. Which Publishers Consistently Beat Industry Averages?
--     Publishers whose catalog avg exceeds overall avg on rating, metacritic, AND popularity.
-- ============================================================================================

WITH industry_avg AS (
    SELECT
        AVG(user_rating)     AS avg_rating,
        AVG(metacritic)      AS avg_meta,
        AVG(popularity_score) AS avg_pop
    FROM game_data
)
SELECT
    gd.publishers,
    COUNT(gd.game_id)                                 AS total_games,
    ROUND(AVG(gd.user_rating)::numeric, 2)            AS avg_user_rating,
    ROUND(AVG(gd.metacritic)::numeric, 2)             AS avg_metacritic,
    ROUND(AVG(gd.popularity_score)::numeric, 2)       AS avg_popularity
FROM game_data gd
GROUP BY gd.publishers
HAVING
    COUNT(gd.game_id) >= 5
    AND AVG(gd.user_rating)      > (SELECT avg_rating FROM industry_avg)
    AND AVG(gd.metacritic)       > (SELECT avg_meta   FROM industry_avg)
    AND AVG(gd.popularity_score) > (SELECT avg_pop    FROM industry_avg)
ORDER BY avg_popularity DESC;


-- ============================================================
-- Q7. Which Publishers Overperform Despite Smaller Catalogs?
--     Small publishers (< median game count) with
--     above-average popularity — quality-over-quantity studios.
-- ============================================================

SELECT
    publishers,
    COUNT(game_id) AS total_games,
    ROUND(AVG(popularity_score)::numeric, 2) AS avg_popularity,
    ROUND(AVG(user_rating)::numeric, 2) AS avg_rating
FROM game_data
GROUP BY publishers
HAVING COUNT(game_id) < 10
   AND AVG(popularity_score) >
       (SELECT AVG(popularity_score)
        FROM game_data)
ORDER BY avg_popularity DESC
LIMIT 15;

-- ============================================================
-- Q8. Do Critics Predict Commercial Success?
--     Correlation-proxy: bucket metacritic scores into bands
--     and compare avg popularity — does high critic score
--     translate to commercial reach?
-- ============================================================

SELECT
    CASE
        WHEN metacritic >= 85 THEN 'Acclaimed (85+)'
        WHEN metacritic >= 70 THEN 'Good (70–84)'
        WHEN metacritic >= 55 THEN 'Mixed (55–69)'
        ELSE                       'Poor (<55)'
    END AS critic_band,
    COUNT(*)                                           AS total_games,
    ROUND(AVG(popularity_score)::numeric, 2)           AS avg_popularity,
    ROUND(AVG(user_rating)::numeric, 2)                AS avg_user_rating,
    ROUND(AVG(ratings_count)::numeric, 2)              AS avg_ratings_count
FROM game_data
WHERE metacritic IS NOT NULL
GROUP BY critic_band
ORDER BY avg_popularity DESC;


-- ============================================================
-- Q9. Which Games Have the Largest Critic–Player Disagreement?
--     ABS(metacritic - user_rating*10) as discord score —
--     surfaces games critics and players see very differently.
-- ============================================================

SELECT
    game_id,
    title,
    publishers,
    metacritic,
    ROUND(user_rating::numeric, 2)                    AS user_rating,
    ROUND((user_rating * 10)::numeric, 2)             AS user_rating_scaled,
    ROUND(ABS(metacritic - user_rating * 10)::numeric, 2)
        AS critic_player_gap,
    CASE
        WHEN metacritic > user_rating * 10 THEN 'Critics Prefer It'
        ELSE 'Players Prefer It'
    END AS who_prefers
FROM game_data
WHERE metacritic IS NOT NULL
ORDER BY critic_player_gap DESC
LIMIT 20;

select * FROM game_data
-- ============================================================
-- Q10. Which Genres Benefit Most From Multiplayer?
--      Compare avg popularity for multiplayer vs single-player
--      within each genre — informs feature investment.
-- ============================================================

SELECT
    gt.genre,
    ROUND(AVG(CASE WHEN gd.is_multiplayer = '1' THEN gd.popularity_score END)::numeric, 2)
        AS multiplayer_popularity,
    ROUND(AVG(CASE WHEN gd.is_multiplayer = '0' THEN gd.popularity_score END)::numeric, 2)
        AS singleplayer_popularity,
    ROUND((
        AVG(CASE WHEN gd.is_multiplayer = '1' THEN gd.popularity_score END) -
        AVG(CASE WHEN gd.is_multiplayer = '0' THEN gd.popularity_score END)
    )::numeric, 2) AS multiplayer_boost
FROM game_data gd
JOIN genre_table gt ON gd.game_id = gt.game_id
GROUP BY gt.genre
ORDER BY multiplayer_boost DESC;


-- ============================================================
-- Q11. Which Platform Release Strategy Is Associated With Stronger Game Performance?
--      Bucket games by platform count (exclusive, mid, wide)
--      and compare popularity + ratings to find the sweet spot.
-- ============================================================

SELECT
    CASE
        WHEN platform_count = 1 THEN 'Exclusive (1 platform)'
        WHEN platform_count BETWEEN 2 AND 4 THEN 'Mid-Range (2–4)'
        ELSE 'Wide Release (5+)'
    END AS platform_strategy,
    COUNT(*)                                           AS total_games,
    ROUND(AVG(popularity_score)::numeric, 2)           AS avg_popularity,
    ROUND(AVG(user_rating)::numeric, 2)                AS avg_user_rating,
    ROUND(AVG(ratings_count)::numeric, 2)              AS avg_ratings_count,
    ROUND(AVG(engagement_score)::numeric, 2)           AS avg_engagement
FROM game_data
GROUP BY platform_strategy
ORDER BY avg_popularity DESC;


-- ============================================================
-- Q12. Which Individual Platforms Host the Best-Rated Games?
--      Rank platforms by the average quality of games on them —
--      useful for platform partnership decisions.
-- ============================================================

SELECT
    pt.platform,
    COUNT(DISTINCT gd.game_id)                        AS total_games,
    ROUND(AVG(gd.user_rating)::numeric, 2)            AS avg_user_rating,
    ROUND(AVG(gd.metacritic)::numeric, 2)             AS avg_metacritic,
    ROUND(AVG(gd.popularity_score)::numeric, 2)       AS avg_popularity
FROM game_data gd
JOIN platform_table pt ON gd.game_id = pt.game_id
GROUP BY pt.platform
HAVING COUNT(DISTINCT gd.game_id) >= 10
ORDER BY avg_user_rating DESC;


-- ============================================================
-- Q13. How Have Genre Release Volumes Changed Over the Last Decade?
--      Compare 2016–2020 vs 2021–2025 release counts per genre
--      to identify trending and declining genres.
-- ============================================================

WITH genre_growth AS (
    SELECT
        gt.genre,
        COUNT(CASE WHEN gd.release_year BETWEEN 2016 AND 2020 THEN gd.game_id END) AS old_games,
        COUNT(CASE WHEN gd.release_year BETWEEN 2021 AND 2025 THEN gd.game_id END) AS new_games
    FROM game_data gd
    JOIN genre_table gt ON gd.game_id = gt.game_id
    GROUP BY gt.genre
)
SELECT
    genre,
    old_games,
    new_games,
    ROUND(((new_games - old_games)::numeric / NULLIF(old_games, 0)) * 100, 2)
        AS growth_pct
FROM genre_growth
ORDER BY growth_pct DESC;


-- ============================================================
-- Q14. Which Tags Are Associated With Top-Rated Games?
--      Find tags that appear frequently in high-rated games —
--      reveals design elements that resonate with players.
-- ============================================================

WITH top_rated AS (
    SELECT game_id
    FROM game_data
    WHERE user_rating >= (
        SELECT PERCENTILE_CONT(0.80) WITHIN GROUP (ORDER BY user_rating) FROM game_data
    )
)
SELECT
    tt.tag,
    COUNT(DISTINCT tt.game_id)                        AS games_with_tag,
    ROUND(AVG(gd.user_rating)::numeric, 2)            AS avg_user_rating,
    ROUND(AVG(gd.popularity_score)::numeric, 2)       AS avg_popularity
FROM tag_table tt
JOIN game_data gd ON tt.game_id = gd.game_id
WHERE tt.game_id IN (SELECT game_id FROM top_rated)
GROUP BY tt.tag
HAVING COUNT(DISTINCT tt.game_id) >= 5
ORDER BY avg_user_rating DESC
LIMIT 20;


-- ============================================================
-- Q15. Which Stores Carry the Highest-Performing Games?
--      Rank stores by the avg quality of their catalog —
--      guides store listing strategy for publishers.
-- ============================================================

SELECT
    st.store,
    COUNT(DISTINCT gd.game_id)                        AS total_games,
    ROUND(AVG(gd.user_rating)::numeric, 2)            AS avg_user_rating,
    ROUND(AVG(gd.popularity_score)::numeric, 2)       AS avg_popularity,
    ROUND(AVG(gd.engagement_score)::numeric, 2)       AS avg_engagement
FROM game_data gd
JOIN store_table st ON gd.game_id = st.game_id
GROUP BY st.store
HAVING COUNT(DISTINCT gd.game_id) >= 10
ORDER BY avg_popularity DESC;


-- ============================================================
-- Q16. Are Overrated Games Commercially Successful?
--      Define "overrated" as metacritic >> user_rating.
--      Check whether critic hype converts to popularity.
-- ============================================================


WITH rated_games AS (
    SELECT
        game_id,
        title,
        publishers,
        metacritic,
        user_rating,
        popularity_score,
        engagement_score,

        ROUND((metacritic - user_rating * 20)::numeric,2) AS rating_gap

    FROM game_data
    WHERE metacritic IS NOT NULL
      AND user_rating IS NOT NULL
)

SELECT
    CASE
        WHEN rating_gap > 15
            THEN 'Critics Rate Higher'
        WHEN rating_gap < -15
            THEN 'Players Rate Higher'
        ELSE 'Similar Ratings'
    END AS rating_category,

    COUNT(*) AS total_games,

    ROUND(AVG(popularity_score)::numeric, 2) AS avg_popularity,
    ROUND(AVG(engagement_score)::numeric, 2) AS avg_engagement
	FROM rated_games

GROUP BY
    CASE
        WHEN rating_gap > 15
            THEN 'Critics Rate Higher'
        WHEN rating_gap < -15
            THEN 'Players Rate Higher'
        ELSE 'Similar Ratings'
    END

ORDER BY avg_popularity DESC;


-- ============================================================
-- Q17. What Is the Year-over-Year Release Trend by Genre?
--      Track how each genre's annual output has shifted —
--      useful for spotting saturation or emerging markets.
-- ============================================================

SELECT
    gd.release_year,
    gt.genre,
    COUNT(DISTINCT gd.game_id)                        AS games_released,
    ROUND(AVG(gd.popularity_score)::numeric, 2)       AS avg_popularity
FROM game_data gd
JOIN genre_table gt ON gd.game_id = gt.game_id
WHERE gd.release_year >= 2015
GROUP BY gd.release_year, gt.genre
ORDER BY gd.release_year DESC, games_released DESC;


-- ============================================================
-- Q18. Which Games Rank in the Top 5 Per Genre by Popularity?
--      Use window functions to surface the best game in each
--      genre — a genre leaderboard for competitive benchmarking.
-- ============================================================

WITH genre_ranks AS (
    SELECT
        gd.game_id,
        gd.game_name,
        gd.publisher,
        gt.genre,
        gd.popularity_score,
        gd.user_rating,
        RANK() OVER (PARTITION BY gt.genre ORDER BY gd.popularity_score DESC) AS rank_in_genre
    FROM game_data gd
    JOIN genre_table gt ON gd.game_id = gt.game_id
)
SELECT *
FROM genre_ranks
WHERE rank_in_genre <= 5
ORDER BY genre, rank_in_genre;


-- ============================================================
-- Q19. What Predicts Engagement? (Multi-Factor Bucketing)
--      Segment games by playtime + multiplayer flag and measure
--      avg engagement — helps model what drives deep play.
-- ============================================================

SELECT
    CASE
        WHEN avg_playtime_hours >= 50 THEN 'Long (50h+)'
        WHEN avg_playtime_hours >= 20 THEN 'Mid (20–49h)'
        ELSE 'Short (<20h)'
    END AS playtime_bucket,
    CASE
        WHEN is_multiplayer = '1' THEN 'Multiplayer'
        ELSE 'Single-player'
    END AS mode,
    COUNT(*)                                           AS total_games,
    ROUND(AVG(engagement_score)::numeric, 2)           AS avg_engagement,
    ROUND(AVG(popularity_score)::numeric, 2)           AS avg_popularity,
    ROUND(AVG(ratings_count)::numeric, 2)              AS avg_ratings_count
FROM game_data
GROUP BY playtime_bucket, mode
ORDER BY avg_engagement DESC;


-- ============================================================
-- Q20. Which Publishers Have the Most Consistent Quality?
--      Low standard deviation in user_rating = consistent
--      publisher. High avg + low spread = the gold standard.
-- ============================================================

SELECT
    publisher,
    COUNT(game_id)                                     AS total_games,
    ROUND(AVG(user_rating)::numeric, 2)                AS avg_rating,
    ROUND(STDDEV(user_rating)::numeric, 2)             AS rating_stddev,
    ROUND(MIN(user_rating)::numeric, 2)                AS min_rating,
    ROUND(MAX(user_rating)::numeric, 2)                AS max_rating
FROM game_data
GROUP BY publisher
HAVING COUNT(game_id) >= 5
ORDER BY rating_stddev ASC, avg_rating DESC
LIMIT 20;



SELECT
    publishers,
    COUNT(DISTINCT game_id) AS total_games
FROM game_data
GROUP BY publishers
ORDER BY total_games DESC
LIMIT 10;