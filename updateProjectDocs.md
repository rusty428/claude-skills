---
name: updateProjectDocs
description: Generate or update project-level documentation — analyzes project structure, components, and architecture to produce README.md and DEVELOPER.md. Use when a project needs comprehensive documentation.
---
<!-- Version: 2026-03-08.1 -->

# Update Project Documentation

Create comprehensive project-level documentation by analyzing the project structure and components.

## Analysis Steps

1. **Scan project components** to:
   - Read existing documentation files
   - Extract component purposes and workflows
   - Identify the processing pipeline or data flow
   - Map inter-component dependencies and triggers

2. **Review infrastructure** to understand:
   - Read existing CDK/infra documentation
   - Extract deployment architecture and resource relationships
   - Identify shared infrastructure components
   - Understand environment configurations

3. **Examine project structure** for:
   - Shared libraries or common code
   - Configuration files
   - Deployment patterns
   - Testing strategies

4. **Review existing documentation** to:
   - Preserve important context and decisions
   - Maintain consistent tone and style
   - Update outdated information
   - Fill documentation gaps

## Output: Two Files in the Project Root

### README.md (Stakeholder-focused)
- **Project Title & Description**: Clear title with system context
- **How It Works**: Step-by-step workflow explanation
- **Key Components**: Table with component names, purposes, and documentation links
- **AWS Services Integration**: Services used with their specific roles
- **Technical Implementation**: Key architectural patterns and design decisions
- **Infrastructure**: Link to CDK/infra documentation for deployment details
- **Related Projects**: Links to ecosystem components or dependencies

### DEVELOPER.md (Technical implementation)
- **Development Setup**: Local development environment requirements
- **Project Structure**: Directory organization and conventions
- **Deployment Process**: Deployment steps and environments
- **Shared Resources**: Common infrastructure components
- **Testing Strategy**: How to test individual components and the full system
- **Troubleshooting**: Common issues and debugging approaches
- **Contributing**: Code standards and development workflow

## Style Guidelines

- **Title Format**: `ProjectName — Brief Description`
- **Step-by-Step Workflow**: Use numbered sections for process flows
- **Service Integration**: Use **Service Name**: Description format
- **Technical Patterns**: Use bold headers for key architectural concepts
- **Professional Tone**: Balance technical accuracy with business context
- **Footer**: Include "Last updated: YYYY-MM-DD" timestamp

## Writing Standards

- Use active voice showing who or what performs the action
- Replace subjective descriptors with concrete examples and measurements
- Include specific technical details, error messages, file paths, and metrics
- Eliminate hedge phrases and disclaimers that weaken statements
- Focus on measurable outcomes and business impact

## Date Stamping

Run `date` to get current date. Include "Last updated: YYYY-MM-DD" footer in both files.
