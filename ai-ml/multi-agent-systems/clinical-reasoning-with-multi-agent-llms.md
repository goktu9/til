# MedCase AI: Multi-Agent LLM Orchestration for Clinical Training

In medical education, a single, monolithic AI agent often struggles to manage content delivery, diagnostic questioning, and pedagogical feedback simultaneously, leading to "model overload". In this project, I improved system stability and educational quality by implementing a multi-agent architecture powered by **Gemini 2.5 Flash**.

### 1. The Problem
Using a single LLM prompt for a complex clinical simulation creates three main issues:
1.  **Incoherent Responses:** The model may confuse its role as a "patient" with its role as a "tutor".
2.  **Logic Burden:** Generating a high-quality MCQ while simultaneously analyzing a case narrative often leads to "hallucinated" options or weak distractors.
3.  **Brittle Behavior:** Small changes in the prompt can break the entire simulation flow.

### 2. The Solution: Decoupled Multi-Agent Architecture
I used **Google’s Agent Development Kit (ADK)** to orchestrate specialized agents. Each agent has a unique system prompt and a narrow scope of responsibility.

```python
# Conceptual Agent Orchestration
# 1. Case Selector: Retrieves random case from MultiCaRe dataset
case_data = selector_agent.get_random_case()

# 2. MCQ Generator: Creates assessment-quality questions based on case
# Separating this reduces the logic burden on the Dialogue agent.
mcq_data = mcq_generator.generate_mcq(case_data)

# 3. Tutor Agent: Provides feedback based on Mode (Hint, Explain, Teach)
# Focusing on "Thinking like a Doctor" logic.
feedback = tutor_agent.get_feedback(user_answer, mode="teach")
```

### 3. Mode-Based Feedback (TutorAgent)
To mimic an expert instructor, I implemented a mode-switching logic within the Tutor Agent:
* **Explain Mode:** Offers a 3-6 bullet point rationale for the correct answer.
* **Teach Mode:** Delivers a structured "micro-lesson" including clinical approach, common pitfalls, and key takeaways.
* **Hint Mode:** Provides 2-3 clinical clues without revealing the answer.

### 4. Key Benefits
* **System Stability:**  Decoupling agents via Google ADK ensures that failure in one agent (e.g., a poorly formatted MCQ) doesn't crash the entire dialogue flow.
* **Pedagogical Accuracy:**  Specialized prompts allow the Tutor Agent to remain supportive and academic without providing direct treatment orders.
* **Gemini 2.5 Flash Performance:**  Leveraging the speed and reasoning depth of Gemini 2.5 Flash allows for real-time, high-quality clinical conversations without the latency of larger models.
