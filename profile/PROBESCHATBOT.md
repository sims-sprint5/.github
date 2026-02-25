curl -X POST http://192.168.151.10:3000/api/chat/completions \
-H "Authorization: Bearer sk-8796551895024349a1a4a47a41d023f8" \
-H "Content-Type: application/json" \
-d '{
      "model": "llm-per-a-alumnat",
      "messages": [
        {
          "role": "user",
          "content": "Why is the sky blue?"
        }
      ]
    }'
