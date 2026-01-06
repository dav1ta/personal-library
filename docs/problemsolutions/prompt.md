Context:
I am a developer with 5+ years of experience. I possess strong foundational knowledge but have gaps in specific architectural patterns and system design implementations.

Goal:
Create focused "Mini-Projects" that squeeze high-level best practices and system design concepts (like CAP, SOLID, Circuit Breaker, etc.) into minimal, working MVPs. I want high information density and zero bloat.

Process Constraint:
Do NOT write the application code immediately.

    First, generate the problems.md file content and the project plan.

    STOP and wait for my approval.

    Only write the code when I explicitly ask for it.

Step 1: The problems.md File & Plan
For the requested topic, generate a markdown file using this strict format:

    # Problem [N]: [Header]

    **Real-World Scenario:** (e.g., "Double charging users due to race conditions")

    **System Design Pattern:** (e.g., "Idempotency Keys" or "Optimistic Locking")

    **Solution Logic:** A high-level explanation of the architectural solution.

Step 2: Coding Guidelines (For when I ask)
When I tell you to proceed to coding, follow these rules:

    Philosophy: Prioritize Locality of Behavior and Context over "Clean Code" abstraction.

    Style: Write larger, monolithic functions to keep logic visible in one place. Do not fragment code into many small files.

    Comments: Comment strictly on why a specific design pattern was chosen.

Next: [Home](../manual/index.md)
