# Custom Instruction for ServiceStack Projects

## General Guidelines
1. For simple tasks, prefer AutoQuery.
2. When tasks become complex, use a custom service.
3. No need to manually register services or handle any auto wire-up - ServiceStack takes care of this automatically.
4. Always provide complete code statements - never give partial code snippets.

## Designing Services
1. Create DTOs (Data Transfer Objects) for each service.
2. Follow REST patterns when designing endpoints.
3. Always assign routes to services.
4. Consider authentication requirements for each route based on the provided information.
5. Every service endpoint must have both a request DTO and a response DTO.

## AutoQuery Usage
- Implement AutoQuery for simple CRUD operations and basic querying needs.
- Utilize AutoQuery attributes and conventions to minimize boilerplate code.

## Custom Services
- Implement custom services for complex business logic, multi-step processes, or operations that don't fit the AutoQuery model.
- Ensure services are focused and adhere to single responsibility principle.
- Remember that ServiceStack automatically discovers and registers your services - no manual registration required.

## DTO and Route Design
1. Use clear, descriptive names for DTOs that reflect their purpose.
2. Design routes following REST conventions:
   - GET for retrieving data
   - POST for creating new resources
   - PUT for updating existing resources
   - DELETE for removing resources
3. Use appropriate HTTP status codes in responses.
4. For each service endpoint:
   - Create a request DTO to define the input parameters
   - Create a response DTO to define the structure of the returned data

## Field Attributes and Validation
1. ID Fields:
   - Always apply [PrimaryKey] attribute to ID fields
   - Always use [AutoIncrement] with [PrimaryKey] unless explicitly specified otherwise

2. Foreign Key Fields:
   - When using [ForeignKey] attribute, always specify appropriate options (e.g., OnDelete, OnUpdate)
   - Always add a [Ref(typeof(ReferencedType))] decorator alongside the [ForeignKey] attribute
   - Include a corresponding Reference property for each foreign key
   - Example:
     ```csharp
     [ForeignKey(typeof(Category), OnDelete = "CASCADE")]
     [Ref(typeof(Category))]
     public int CategoryId { get; set; }

     [Reference]
     public Category Category { get; set; }
     ```

3. String Fields:
   - Try to determine an appropriate length and use [StringLength(n)] where possible
   - If unsure, use [MaxLength(n)] with a reasonable maximum length

4. Decimal Fields:
   - Set decimal precision using [DecimalLength(precision, scale)]
   - Apply any other relevant decorators for decimal fields

5. Validation Attributes:
   When implementing validation, prioritize using the following specific attributes before resorting to the general `[Validate]` attribute or custom validation:

   ```csharp
   public class ExampleValidators : ICreateDb<ExampleValidator>, IReturn<EmptyResponse>
   {
       [ValidateCreditCard]
       public string CreditCard { get; set; }

       [ValidateEmail]
       public string Email { get; set; }

       [ValidateEmpty]
       public string Empty { get; set; }

       [ValidateEqual("Equal")]
       public string Equal { get; set; }

       [ValidateLessThan(10)]
       public int LessThan { get; set; }

       [ValidateLessThanOrEqual(10)]
       public int LessThanOrEqual { get; set; }

       [ValidateGreaterThan(10)]
       public int GreaterThan { get; set; }

       [ValidateGreaterThanOrEqual(10)]
       public int GreaterThanOrEqual { get; set; }

       [ValidateExclusiveBetween(10, 20)]
       public int ExclusiveBetween { get; set; }

       [ValidateInclusiveBetween(10, 20)]
       public int InclusiveBetween { get; set; }

       [ValidateExactLength(10)]
       public string Length { get; set; }

       [ValidateNotEmpty]
       public string NotEmpty { get; set; }

       [ValidateNotEqual("NotEqual")]
       public string NotEqual { get; set; }

       [ValidateNull]
       public string Null { get; set; }

       [ValidateScalePrecision(1,1)]
       public decimal ScalePrecision { get; set; }

       [ValidateRegularExpression("^[a-z]*$")]
       public string RegularExpression { get; set; }
   }
   ```

   If the specific validation attributes don't meet your needs, you can use the general `[Validate]` attribute with a validation expression. Here are examples:

   ```csharp
   public class ExampleValidators : ICreateDb<ExampleValidator>, IReturn<EmptyResponse>
   {
       [Validate("CreditCard")]
       public string CreditCard { get; set; }

       [Validate("Email")]
       public string Email { get; set; }

       [Validate("Empty")]
       public string Empty { get; set; }

       [Validate("Equal('Equal')")]
       public string Equal { get; set; }

       [Validate("ExclusiveBetween(10, 20)")]
       public int ExclusiveBetween { get; set; }

       [Validate("GreaterThanOrEqual(10)")]
       public int GreaterThanOrEqual { get; set; }

       [Validate("GreaterThan(10)")]
       public int GreaterThan { get; set; }

       [Validate("InclusiveBetween(10, 20)")]
       public int InclusiveBetween { get; set; }

       [Validate("ExactLength(10)")]
       public string Length { get; set; }

       [Validate("LessThanOrEqual(10)")]
       public int LessThanOrEqual { get; set; }

       [Validate("LessThan(10)")]
       public int LessThan { get; set; }

       [Validate("NotEmpty")]
       public string NotEmpty { get; set; }

       [Validate("NotEqual('NotEqual')")]
       public string NotEqual { get; set; }

       [Validate("Null")]
       public string Null { get; set; }

       [Validate("RegularExpression('^[a-z]*$')")]
       public string RegularExpression { get; set; }

       [Validate("ScalePrecision(1,1)")]
       public decimal ScalePrecision { get; set; }
   }
   ```

   Request Validation Attributes:
   - [ValidateRequest]: General request validation
   - [ValidateIsAuthenticated]: Protect access to API for Authenticated Users only
   - [ValidateIsAdmin]: Protect access to API for Admin Users only
   - [ValidateHasPermission]: Protect access to API for Users with ALL specified Permissions
   - [ValidateHasRole]: Protect access to API for Users with ALL specified Roles

6. General Practice:
   - Always consider the nature of the data and apply the most appropriate validation decorators
   - Use multiple decorators when necessary to ensure comprehensive validation
   - When in doubt about which decorator to use, consult the Excel spreadsheets
   - Provide complete code statements - never give partial code snippets
   - Prefer specific validation attributes when possible
   - Use the general `[Validate]` attribute with appropriate expressions when specific attributes are not suitable
   - Resort to custom validation logic only when neither specific attributes nor the general `[Validate]` attribute can meet the requirements

## Authentication
- Evaluate each route to determine if it requires authentication.
- Apply authentication attributes as necessary (e.g., `[Authenticate]`, `[RequiredRole("Admin")]`).

## Decorators
- Refer to the provided Excel files for the complete list of available decorators.
- Apply relevant decorators to DTOs and services to enhance functionality and maintain consistency.
- Regularly review the Excel files to stay updated on available decorators.

## Rate Limiting

When implementing rate limiting for your ServiceStack application, especially for SaaS applications with per-user rate limiting, refer to the official ServiceStack documentation:

https://docs.servicestack.net/rate-limiting#per-user-rate-limiting-for-saas-applications

Key points to consider:

1. ServiceStack provides built-in support for rate limiting.
2. You can configure global rate limits or per-user rate limits.
3. Rate limits can be set based on various time intervals (per second, minute, hour, day).
4. For SaaS applications, consider implementing per-user rate limiting to ensure fair usage across your user base.
5. The documentation provides examples of how to configure and customize rate limiting for your specific needs.

Always consult the official documentation for the most up-to-date information and best practices regarding rate limiting in ServiceStack.

## Best Practices
1. Keep services and DTOs in separate files for better organization.
2. Use meaningful names for services, DTOs, and their properties.
3. Include XML comments for public APIs to improve documentation.
4. Implement proper error handling and validation in services.
5. Use dependency injection where appropriate to manage service dependencies.
6. Ensure that request and response DTOs are properly paired for each service endpoint.
7. Apply field-specific attributes and validation consistently across all DTOs.
8. Regularly consult the Excel spreadsheets for the most up-to-date list of decorators and their usage.
9. Trust ServiceStack's auto-discovery and registration - no need to manually register services or configure wire-up.
10. When using foreign keys, always include both [ForeignKey] and [Ref] attributes, and add a corresponding Reference property.
11. Use the provided validation attributes whenever possible before resorting to custom validation logic.
12. Always provide complete code statements, never partial snippets.
13. Use specific validation attributes whenever possible
14. Use the general `[Validate]` attribute with appropriate expressions when specific attributes are not suitable
15. Resort to custom validation logic only when neither specific attributes nor the general `[Validate]` attribute can meet the requirements
16. Implement appropriate rate limiting for your APIs, especially for SaaS applications.
17. Regularly review and adjust rate limits based on your application's needs and user behavior.

## Code Review Checklist
- [ ] AutoQuery used for simple tasks
- [ ] Custom services implemented for complex logic
- [ ] DTOs created and properly structured
- [ ] Each service endpoint has both request and response DTOs
- [ ] REST patterns followed in route design
- [ ] Routes assigned to all services
- [ ] Authentication considered and implemented where necessary
- [ ] Appropriate decorators applied (as per Excel spreadsheets)
- [ ] ID fields have [PrimaryKey] and [AutoIncrement] attributes (unless specified otherwise)
- [ ] String fields use [StringLength] or [MaxLength] appropriately
- [ ] Decimal fields have proper precision set
- [ ] Validation decorators applied based on field names and purposes
- [ ] Excel spreadsheets consulted for most recent decorator information
- [ ] Code is clean, readable, and well-commented
- [ ] Error handling and input validation implemented
- [ ] Request and response DTOs are logically paired for each endpoint
- [ ] No unnecessary manual service registration or wire-up code present
- [ ] Foreign key fields have [ForeignKey] attribute with appropriate options
- [ ] [Ref] decorator is used alongside [ForeignKey] attributes
- [ ] Reference properties are included for all foreign keys
- [ ] Appropriate validation attributes are used for each field
- [ ] Custom validation is only used when necessary
- [ ] All code snippets and examples are complete, not partial
- [ ] Specific validation attributes are used where applicable
- [ ] General `[Validate]` attribute is used appropriately when specific attributes are not suitable
- [ ] Custom validation is only used when absolutely necessary
- [ ] All validation expressions in `[Validate]` attributes are correct and follow ServiceStack conventions
- [ ] Rate limiting is implemented where necessary
- [ ] Per-user rate limiting is considered for SaaS applications
- [ ] Rate limits are appropriate for the application's requirements
