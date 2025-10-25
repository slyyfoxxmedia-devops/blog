Overview and Responsibility The CMS Microservice is the central content hub and the front-facing editor UI for the entire platform. It allows non-technical users (Editors, Marketers) to create and manage content without writing code.
Subdomain: content.slyyfoxxmedia.com

Status: Phase 1: Core Data Foundation (Priority 3)

Single Responsibility: Content creation, content state management (Draft/Live), and API delivery of structured content.

Core Features and Editor ExperienceThis is a Hybrid Microservice hosting both the backend content API and the frontend editor UI.A. Editor Interface (Frontend UI)FeatureImplementation DetailArchitectural LinkNo-Code/Block EditorThe core UI allows drag-and-drop creation using reusable content blocks (widgets).The editable page and preview page are separate files/components.Direct Image ManipulationUsers can drag the corners of an image box to set desired dimensions.The CMS saves the requested size as metadata (e.g., width: 800px) and relies on an Image Optimization Service in the infrastructure to process the image on demand.Content Sizing OptionsOffers predefined styling options (e.g., "Full-Width Banner," "Two-Column Card").The CMS saves the option name (e.g., layout: full-width). The Frontend Microservices (Marketplace/Blog) apply the correct CSS to render it.Live PreviewThe editor UI embeds a separate Preview Frontend (calling the Draft API) for real-time visualization of unsaved content.The Preview page itself is a simple component hosted by another service.
B. Content Modeling (Schemas) The CMS must support structured schemas for different consumers:

Article: (For the Blog) Title, Body, Author, SEO Metadata.

ProductMarketingBlock: (For the Marketplace) Short Description, Testimonial Reference, Product Feature List.

GlobalComponent: (For reusable items) Header/Footer content, Call-to-Action buttons.

FormTemplate: Defines the fields for lead capture forms (data is sent to the CRM).

C. Governance and StateFeatureDesign DetailContent WorkflowMinimal required steps: Draft 
→
 Publish 
→
 Archive. Complex approval logic is delegated to the Project Management Microservice.VersioningStores an infinite history of all changes to content items, allowing rollbacks to any prior published state for auditing.Asset ManagementS3 File Paths: The CMS stores only the URL/path to images/media. The actual files are stored in a dedicated, high-speed object storage service (S3).

Inter-Service Communication (APIs)A. Data Delivery APIsEndpointMethodSecurity/AccessPurpose/v1/content/live/{id}GETPublic Access (high caching)Serves the final, published content to the Marketplace and Blog frontends./v1/content/draft/{id}GETPrivate (SSO-authenticated)Serves unpublished content to the Live Preview component./v1/content/item/{id}POST/PUT/DELETEAdmin/Editor Roles OnlyUsed internally by the CMS editor UI to save or delete content drafts.
B. Event Emitter (Asynchronous Communication) The CMS publishes an event when content is ready for the world to see:

Event: ContentPublishedV1

Subscribers:

Search Microservice: Subscribes to this event to instantly re-index the new content.

Analytics Microservice: Records the event timestamp to measure content velocity.

Email Microservice: (Optional) Could be used to trigger a "New Blog Post Alert" newsletter.

C. Dependencies (Data Input) Input Data: The CMS calls the Product Microservice to get unique SKUs and identifiers so that content can be correctly linked to a product.

User Roles: The CMS relies on the SSO Microservice to read the user's role/tier from the JWT token to enable/disable features like "Premium Templates" or the Branding Removal option.

