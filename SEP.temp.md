# SEP-XXX: Add sandbox enviroment as a optional header

## Preamble
Title: Add sandbox enviroment as a optional header
Authors: Tobin South (@tobinsouth), Darius Emrani (@?)
Status: Proposal
Type: Standards Track SEP

## Abstract
This SEP proposes an enhancement of the specification that allows servers to accept a request for sandbox mode in a standard and interoperable way. This creates a standardized experience for evaluations, testing, and training on MCP server behavior for AI models. 


## Motivation
Traditional API sandboxes provide safe testing environments separate from production systems. MCP (Model Context Protocol) servers require similar sandboxes, but face additional challenges due to their specific workloads. Unlike conventional APIs that handle discrete requests, MCP servers manage persistent contexts, complex tool chains, and multi-step workflows that AI agents navigate during reinforcement learning.
During training, RL agents execute thousands of exploratory actions, combine tools in unexpected ways, and test system boundaries. MCP's stateful nature means agents can trigger cascading operations across multiple tools. Without sandboxes, organizations must either expose production systems to unpredictable agent behavior or limit their development capabilities.
Authentication-protected MCP servers add complexity, as evaluation frameworks must handle OAuth flows, token management, and permission boundaries while maintaining reproducible training conditions. Sandbox environments enable safe, repeatable AI agent development without risking production data, violating compliance requirements, or incurring costs from unbounded agent exploration.










# Example below


SEP-835: Update authorization spec with default scopes definition
Preamble
Title: Update authorization spec with default scopes definition
Author: Den Delimarsky (@localden)
Status: Proposal
Type: Standards Track SEP
Abstract
This SEP proposes enhancements to the MCP OAuth 2.1-based authorization specification to improve scope management, client experience, and support for different client types. The changes include: (1) structured scope selection strategies following the principle of least privilege, (2) comprehensive scope error handling with upgrade flows, (3) differentiated behavior for client credentials vs. interactive clients, and (4) improved consistency in error responses.

Motivation
The current authorization specification lacks detailed guidance for:

Scope selection: Clients don't have clear guidance on which scopes to request initially
Runtime scope upgrades: No standardized flow for handling insufficient scope errors during operation
Client type differences: Client credentials clients have different capabilities than interactive clients but are treated the same
Error response consistency: 403 responses lack resource metadata present in 401 responses, complicating client implementations
These gaps lead to over-privileged token requests, poor user experience, and implementation inconsistencies across MCP clients and servers.

Specification
The specification changes include:

4.1 Scope Selection Strategy
Priority-based scope selection: WWW-Authenticate scope parameter first, fallback to scopes_supported in PRM
Principle of least privilege guidance
Flexibility for out-of-band scope decisions
4.2 Enhanced Error Handling
Standardized 403 insufficient_scope responses with resource_metadata
Scope upgrade flow with differentiated client behavior:
Interactive clients SHOULD attempt upgrade
Client credentials clients MAY attempt or abort immediately
Retry limits and caching to prevent infinite loops
4.3 Security Enhancements
New Scope Minimization security section
Guidance on fine-grained scopes
Progressive access patterns support
Rationale
Scope Selection: The priority-based approach balances security (least privilege) with usability. The WWW-Authenticate scope parameter provides immediate, contextual guidance while scopes_supported offers fallback coverage.

Client Type Differentiation: Client credentials clients lack interactive authorization capabilities, making automatic scope upgrade less practical. The MAY vs SHOULD distinction reflects this reality while still allowing implementation flexibility.

Error Response Consistency: Including resource_metadata in 403 responses matches 401 behavior, simplifying client implementations by making error responses self-contained.

Removed Restrictive Logic: Original draft prevented reauthorization in scenarios where it might succeed (timed access, conditional policies). The retry limits provide sufficient protection against infinite loops without being overly restrictive.

Backward Compatibility
This SEP is backward compatible. All changes are additive or clarifications:

New scope selection behavior is recommended (SHOULD) not required (MUST)
Error handling enhancements are optional for servers
Existing client implementations continue to work unchanged
No breaking changes to protocol messages or flow
Reference Implementation
Update MCP specification documentation
Example client implementations demonstrating scope selection strategies
Example server implementations showing enhanced error responses
Test cases covering upgrade flows and edge cases
Security Implications
Positive impacts:

Reduced token privilege through better scope selection
Improved error handling reduces attack surface
Clear guidance prevents security antipatterns
Considerations:

Scope upgrade flows must implement proper retry limits
Caching mechanisms should not leak authorization state
Client credentials flow limitations are clearly documented
Community Impact
Client developers: Clearer implementation guidance, better user experience
Server developers: Standardized error responses, security best practices
End users: Reduced permission prompts, more secure access patterns
Ecosystem: Better interoperability through consistent behavior
This SEP qualifies as a Standards Track SEP because it proposes significant enhancements to the MCP authorization protocol that will affect multiple implementations and improve ecosystem






Sandbox environments are always separate issuers/URLs 

Distinct API keys (sk_test_ vs sk_live_).

environment

"env": "sandbox"

The goal here is to minimize the work required by resource server developers to support simulation. 


We want to avoid resource abuse, especially in the case of significant use for model training. 

