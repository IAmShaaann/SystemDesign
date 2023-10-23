# How will you design ChatGPT?

Functional Requirements -

- You should be able to communicate with ChatGPT
  - Create conversations.
  - Update (existing messages)
  - Read Chats
  - Delete Chats
- Response to answers (thumbs-UP/DOWN)

Non Functional Requirements

- Latency → 5 seconds to generate response.
- Security → Login, Rate limiting(Prevent DDoS)
- Scalable
  - Requests ⇒ 10M Users/day → 5 Conversation → Each conversations has 4 messages = 200M Messages/day.
  - Storage ⇒ 200M Messages/day → Each message 100 bytes → 20GB/day
