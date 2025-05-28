import express from 'express';
import cors from 'cors';
import dotenv from 'dotenv';
import { OpenAI } from 'openai';

dotenv.config();
const app = express();
app.use(cors());
app.use(express.json());

const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

app.post('/chat', async (req, res) => {
  const userMessage = req.body.message;

  try {
    const chat = await openai.chat.completions.create({
      model: 'gpt-4o',
      messages: [
        {
          role: 'system',
          content: 'You are the official AI assistant for PayGlobalCoin. Answer only crypto-related questions. Politely reject unrelated topics.'
        },
        { role: 'user', content: userMessage }
      ],
      temperature: 0.7
    });

    const reply = chat.choices[0].message.content;
    res.json({ reply });
  } catch (err) {
    console.error('OpenAI error:', err.message);
    res.status(500).json({ error: 'Something went wrong.' });
  }
});

app.listen(3000, () => {
  console.log('✅ PGC AI Server running on port 3000');
});
