# Candidate Dropbox API  
**External Test & Integration Guide (Backend Email Only)**

This API accepts a **public resume PDF**, generates a structured candidate review, and **emails the review directly from the backend** to the specified recipient.

This guide is written so **any tester (technical or non-technical)** can successfully test the API.

---

## 🔍 What We’re Testing (Please Read First)

This testing phase is **invite-only** and intentionally limited to a small group (≈10 people).

### The goal of this test
We are evaluating the **quality of the candidate review**, specifically:

- Does the review reflect how *you* would reason as an interviewer?
- Does it surface the right signals for real hiring conversations?
- Where does it feel:
  - shallow
  - overconfident
  - missing important context
  - misaligned with how you personally judge candidates?

Think of this as:

> *“Would this review meaningfully help me prepare for or conduct a real interview?”*

---

### What to expect in this phase

  -The output is a review aide, meant to help interviewers form better questions and spot gaps.
  
  -It is not a final decision or replacement for interviews.
  
  -We’re intentionally optimizing for judgment quality over feature completeness.
  
  -The scope is intentionally narrow so we can improve **judgment quality**, not automation.

---

### How to give the most useful feedback
When reporting issues or feedback, focus on **review substance**, for example:

- “This conclusion feels too strong given the evidence.”
- “I would probe X in an interview, but it’s missing.”
- “The review focuses on signals I don’t personally value.”
- “This part matched my own assessment closely.”

Both **positive and critical** feedback are equally valuable.

---

### Who this test is for
This test is best suited for people who:

- Interview candidates regularly, or  
- Review resumes / profiles as part of hiring, or  
- Have strong opinions on what makes a candidate interview-ready  

If that’s you — your judgment is exactly what we’re trying to learn from.

---

### Thank you
Your feedback directly shapes how this review system improves.

This is an early system, and **thoughtful critique matters far more than volume**.

---

## 1) Base URL

