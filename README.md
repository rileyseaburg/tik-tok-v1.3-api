# TikTok API OpenAPI Specification

## Schema Documentation Structure

The OpenAPI specification is organized into the following structure:

1. Main swagger.yaml
- References endpoint files for paths
- References common types and responses
- Defines security schemes
- Example:
```yaml
paths:
  /v1.3/ad/get:
    $ref: './endpoints/ad/get.yaml'

components:
  schemas:
    Timestamp:
      $ref: './endpoints/schemas/types.yaml#/components/schemas/Timestamp'
  responses:
    BadRequest:
      $ref: './endpoints/schemas/responses.yaml#/components/responses/BadRequest'
```

2. Endpoint YAML Files
- Located in endpoint-specific directories (e.g., ad/get.yaml)
- Define endpoint operations (GET, POST, etc.)
- Contain inline parameter schemas
- Reference common types for shared schemas
- Reference standard responses
- Example:
```yaml
get:
  parameters:
    - name: advertiser_id
      in: query
      required: true
      schema:
        type: string
  responses:
    '200':
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/AdResponse'
    '400':
      $ref: '#/components/responses/BadRequest'
```

3. types.yaml
- Located in endpoints/schemas/
- Common type definitions
- Enum values
- Object schemas
- Shared components
- Example:
```yaml
components:
  schemas:
    AdFormat:
      type: string
      description: The format of the ad
      enum:
        - SINGLE_IMAGE
        - SINGLE_VIDEO
        - LIVE_CONTENT
```

4. responses.yaml
- Located in endpoints/schemas/
- Standard response structures
- Error handling schemas
- Endpoint-specific response objects
- Reusable components
- Example:
```yaml
components:
  responses:
    BadRequest:
      description: Bad Request
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
```

### Schema Documentation Standards

1. Response Schemas
- Must define all possible response fields
- Include proper types and formats
- Document nullable fields
- Provide example values
- Include error scenarios

2. Common Types
- Use consistent naming conventions
- Include comprehensive descriptions
- Document all enum values
- Specify format constraints
- Add relevant examples

3. Version Compatibility
- Note v1.2 vs v1.3 differences
- Document deprecated fields
- Specify new fields
- Include migration notes

### Best Practices

1. File Organization
- Keep endpoint files in feature-specific directories
- Store common schemas in schemas directory
- Use clear, descriptive file names
- Maintain consistent directory structure

2. Schema References
- Use relative paths for references
- Keep common types in types.yaml
- Keep response structures in responses.yaml
- Use inline schemas for endpoint-specific parameters
- Reference common schemas from swagger.yaml

3. Documentation
- Provide clear descriptions for all fields
- Include examples where helpful
- Document required vs optional fields
- Explain enum values and constraints
- Add notes about dependencies or requirements

4. Version Control
- Document breaking changes
- Maintain backward compatibility
- Use semantic versioning
- Include migration guides

## Using Embeddings for Knowledge Retrieval

This project uses embeddings to store and retrieve implementation knowledge. The embeddings-server allows for semantic search of our documentation, making it easy to find relevant information about patterns, implementations, and best practices.

### Storing Knowledge

When implementing new endpoints or patterns, store the knowledge using embeddings:

```javascript
use_mcp_tool({
  server_name: "embeddings-server",
  tool_name: "store_embedding",
  arguments: {
    id: "endpoint_video_list",
    text: "Video List endpoint implementation details...",
    metadata: {
      type: "endpoint",
      category: "video",
      version: "v1.3"
    }
  }
});
```

### Finding Relevant Information

Use similarity search to find relevant documentation:

1. Implementation Patterns:
```javascript
use_mcp_tool({
  server_name: "embeddings-server",
  tool_name: "similarity_search",
  arguments: {
    text: "How to implement a new endpoint with inline schemas",
    limit: 5
  }
});
```

2. Endpoint Examples:
```javascript
use_mcp_tool({
  server_name: "embeddings-server",
  tool_name: "similarity_search",
  arguments: {
    text: "video list endpoint implementation example",
    limit: 3
  }
});
```

3. Documentation Standards:
```javascript
use_mcp_tool({
  server_name: "embeddings-server",
  tool_name: "similarity_search",
  arguments: {
    text: "documentation requirements for endpoints",
    limit: 5
  }
});
```

### Stored Knowledge Categories

We've embedded documentation for:

1. Project Structure
- File organization patterns
- Directory conventions
- Schema management rules

2. Implementation Examples
- Business Profile endpoint
- Video List endpoint
- Video Settings endpoint
- Comment List endpoint

3. Standards and Rules
- OpenAPI Schema rules
- Documentation requirements
- Testing procedures
- Version control practices

4. Relationships and Dependencies
- Endpoint dependencies
- Component interactions
- Feature relationships

### Best Practices for Using Embeddings

1. Be Specific in Queries
   - Instead of "endpoint", use "video list endpoint implementation"
   - Instead of "schemas", use "inline schema patterns for endpoints"

2. Store Comprehensive Knowledge
   - Include full implementation details
   - Add context and relationships
   - Document edge cases and gotchas

3. Use Metadata Effectively
   - Tag content by type (endpoint, pattern, standard)
   - Include version information
   - Add category classifications

4. Regular Updates
   - Store new knowledge as it's created
   - Update existing embeddings when patterns evolve
   - Remove outdated information

### Example Knowledge Queries

1. Finding Implementation Patterns:
```javascript
use_mcp_tool({
  server_name: "embeddings-server",
  tool_name: "similarity_search",
  arguments: {
    text: "How to structure a new endpoint file with inline schemas",
    limit: 3
  }
});
```

2. Understanding Error Handling:
```javascript
use_mcp_tool({
  server_name: "embeddings-server",
  tool_name: "similarity_search",
  arguments: {
    text: "error response patterns and status codes",
    limit: 5
  }
});
```

3. Checking Documentation Requirements:
```javascript
use_mcp_tool({
  server_name: "embeddings-server",
  tool_name: "similarity_search",
  arguments: {
    text: "required documentation sections for new endpoints",
    limit: 3
  }
});
```

### Development Workflow with Embeddings

1. Before implementing:
   - Search for similar endpoints
   - Look up implementation patterns
   - Check documentation requirements

2. During implementation:
   - Reference stored examples
   - Verify schema patterns
   - Check error handling approaches

3. After implementation:
   - Store new implementation details
   - Document unique patterns or solutions
   - Update related knowledge

## Resources

- [TikTok Business API Documentation](https://business-api.tiktok.com/portal/docs)
- [OpenAPI 3.0.0 Specification](https://spec.openapis.org/oas/v3.0.0)
- [Swagger UI](https://swagger.io/tools/swagger-ui/)
- [Embeddings Server Documentation](https://modelcontextprotocol.io)

### Quick Start - Swagger UI

To quickly view the API documentation in a Swagger UI, run the following command:

```
docker run -p 8080:8080 -e SWAGGER_JSON=/foo/swagger.yaml -v ${PWD}:/foo swaggerapi/swagger-ui
```

This command starts a Docker container with the Swagger UI, mounts the current directory to `/foo` in the container, and sets the `SWAGGER_JSON` environment variable to `/foo/swagger.yaml`. The UI will be accessible at `http://localhost:8080`.

## Client Generation

The TypeScript client in the `client` directory is generated using the [OpenAPI Generator](https://openapi-generator.tech/). This tool automatically generates API clients from OpenAPI specifications.

The `openapitools.json` file in the root directory indicates that the project uses `@openapitools/openapi-generator-cli` version `7.10.0`. However, specific configuration details for the client generation are likely handled externally or through a different configuration file.