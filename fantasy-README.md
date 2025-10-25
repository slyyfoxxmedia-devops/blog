A. Core Data and LogicFeatureDesign Detail (Proposal)Rationale1. Unique IdentifierLeague ID (UUID/alphanumeric string).The unique ID for each game instance. The User ID (from SSO) is secondary and used for team ownership within the league.2. Logic EngineBuilt-in Rules Engine (Local Code).Autonomy: The scoring and rule logic (the "game") is the primary function of this microservice. It should be written directly into the service's code to keep it fully independent and optimized for speed on game day.3. Real-time Data SourceExternal API Ingestion Service. The Fantasy service calls a small, dedicated local wrapper (a simple function within the microservice) that handles rate limiting and formatting the data from Yahoo/other sports APIs.Decoupling: Protects the core scoring logic from API rate limit errors and ensures the data format is consistent, regardless of the external provider.

B. Communication and IntegrationFeatureDesign Detail (Proposal)Rationale4. User AccessAPI Call to SSO: GET /v1/sso/user/{user_id}/claims.Identity: Fantasy service relies entirely on the SSO Microservice to provide the user_id, subscription_tier, and has_fantasy_access claims to determine eligibility.5. Game State UpdateAsynchronous Event: Emits Event: LeagueStatusUpdatedV1.Engagement: When a championship is won or a score is finalized, this event is broadcast. The Social-Tech Microservice listens for this event to generate a post on the user's social feed.6. Admin ManagementAPI Call: POST /v1/leagues/{id}/reset-score (Secure, Admin-only).Control: The Admin Microservice calls this endpoint to manually intervene, correct scores, or close a season. Access is enforced by the API Gateway checking for the SLYYFOXX_ADMIN role in the JWT.

C. Scalability and BillingFeatureDesign Detail (Proposal)Rationale7. Scaling StrategyElastic Compute (AWS Fargate/Lambda).Cost & Burst: Scoring and calculations are high-compute but bursty (only intense during games). Using serverless compute resources allows the service to scale massively on game day and scale completely to zero afterward, saving significant cost.8. Entitlements and BillingSSO Token Claim Check. The Fantasy service checks the user's JWT for the subscription_tier or a specific fantasy_access_token claim.Performance: Checking the JWT is instant (local token decoding). It avoids making a slow database call to the Subscriptions Microservice on every game-related action.

About
fantasy sports micro service for baseball, basketball and football

Resources
 Readme
 Activity
Stars
 0 stars
Watchers
 0 watching
Forks
 0 forks
Releases
No releases published
Create a new release
Packages
No packages published
Publish your first package
Footer
© 2025 GitHub, Inc.
Footer navigation
Terms
