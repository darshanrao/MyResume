# Olympics Highlights Generator — STAR Report

## Situation

The 2024 Summer Olympics, a global event watched by millions, highlighted a challenge: fans, broadcasters, and content creators want to relive and share the most thrilling moments quickly and in a personalized way. Traditional highlight generation is slow, labor-intensive, and limited by manual editing, making it difficult to deliver engaging, tailored content at scale.

---

## Task

**Objective:** Build an AI-powered system that:
- Accepts a natural language prompt from the user describing the type of Olympic highlights they want.
- Automatically generates a script, voice-over, and a synchronized highlight reel using curated Olympic footage.
- Delivers a professional-quality, 90-second personalized highlight video, streamlining the entire process for both consumers and content producers.

---

## Action

To achieve this, we designed and implemented a multi-stage workflow, integrating several advanced AI models and APIs, and wrapped the process in a user-friendly web interface. Here's how the system works:

### 1. User Prompt Decomposition
- **Input:** User provides a natural language prompt (e.g., "Create a 90-second highlight about Turkish Sharpshooter Yusuf Dikec…").
- **Process:**  
  - The prompt is sent to the `Decomposer` class, which uses the Gemini model to break the prompt into:
    - A time limit (e.g., 1:30)
    - Multiple focused queries (e.g., "Highlight Yusuf Dikec's shooting style", "Compare him to traditional competitors")
- **Output:** List of queries + time limit.

### 2. Video Clip Search & Extraction
- **Input:** List of queries from step 1.
- **Process:**  
  - The `TwelveLabsSearch` class uses the Marengo model (TwelveLabs API) to search a curated Olympic video index for each query.
  - For each result, the relevant video segment is downloaded and cropped.
  - All clips are merged into a single video.
- **Output:** A merged video file containing all relevant highlights.

### 3. Video Upload
- **Input:** Merged video file.
- **Process:**  
  - The merged video is uploaded to the TwelveLabs index for further processing.
- **Output:** Video ID for the uploaded video.

### 4. Script Generation (Pegasus/Gemini)
- **Input:** Video ID, user prompt, and time limit.
- **Process:**  
  - The `summarize_video` function is called.
  - It first attempts to generate a rough commentary for the video (using a summarization model, e.g., Pegasus).
  - The rough script is then **improved** using the Gemini model, which:
    - Refines the script for fluency, engagement, and alignment with the user's prompt and time limit.
    - Ensures the script is continuous, engaging, and within the word/time constraints.
- **Output:** A polished, engaging commentary script.

### 5. Text-to-Speech (Voice-Over)
- **Input:** Final script from step 4.
- **Process:**  
  - The script is sent to the ElevenLabs API to generate a realistic voice-over.
  - The audio speed is adjusted to fit the time limit if needed.
- **Output:** Voice-over audio file.

### 6. Video & Audio Synchronization
- **Input:** Merged video and voice-over audio.
- **Process:**  
  - The system synchronizes the audio and video, ensuring the narration matches the visuals.
- **Output:** Final highlight video.

### 7. Output Delivery
- **Input:** Final video file.
- **Process:**  
  - The video is uploaded to Firebase storage.
  - A signed URL is generated and returned to the user via the FastAPI endpoint.
- **Output:** Downloadable/shareable link to the personalized highlight reel.

---

### **Pipeline Table: Steps & Models**

| Step                        | Model/API Used         | File(s) Involved                                 |
|-----------------------------|------------------------|--------------------------------------------------|
| Prompt Decomposition        | Gemini                 | marengo_text_search/decomposer.py                |
| Video Search & Extraction   | Marengo (TwelveLabs)   | marengo_text_search/marengo_search.py, clip_fetch_and_synthesis.py |
| Script Generation           | Pegasus/Gemini         | genScript.py, gemini_utils.py                    |
| Text-to-Speech              | ElevenLabs             | text2speech.py                                   |
| Video Editing/Synchronization| Custom (moviepy, etc.)| combine_audio_video.py, video_edit/moviepy_edit.py|
| Output Delivery             | Firebase, FastAPI      | firebase_utils.py, api.py                        |

---

## Result

- **Automated Highlight Generation:**  
  The system can generate dynamic, professional-quality Olympic highlight reels from simple user prompts, automating what was previously a manual, time-consuming process.
- **High-Quality Output:**  
  The voice-over and video synchronization exceeded expectations, delivering engaging, personalized content.
- **User-Friendly & Scalable:**  
  The web-based interface and API-driven backend make the system accessible and scalable for a wide range of users.
- **Learning & Innovation:**  
  The project deepened our understanding of video editing, AI model integration, and real-time content generation.

---

## What's Next

- **Real-Time Highlights:**  
  Expand to support live Olympic events, enabling real-time highlight generation.
- **Advanced Personalization:**  
  Allow users to specify tone, focus on specific athletes/events, and further tailor their highlight reels.
- **Broader Application:**  
  Adapt the system for other global sporting events and expand the video index to include more sports.

---

**Summary:**  
The Olympics Highlights Generator leverages state-of-the-art AI to transform the way Olympic moments are experienced and shared, making highlight creation faster, smarter, and more personal than ever before. 
