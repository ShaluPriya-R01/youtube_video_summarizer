# YouTube Video Summarizer

Welcome to the YouTube Video Summarizer app! This application allows you to extract transcripts from YouTube videos and generate concise summaries using Google Gemini's language model.

## Features

- **Transcript Extraction:** Retrieve transcripts from YouTube videos using the YouTube Transcript API.
- **Summary Generation:** Use Google Gemini's Generative AI model to generate concise summaries of YouTube video content.
- **User-Friendly Interface:** An intuitive Streamlit-based web interface for easy interaction.


## Installation

### Prerequisites

- Python 3.7+
- [pip](https://pip.pypa.io/en/stable/installation/) package manager
- [Google Gemini API Key](https://console.cloud.google.com/apis/credentials)

### Setup

1. **Clone the repository:**

    ```bash
    git clone https://github.com/your-username/youtube-video-summarizer.git
    cd youtube-video-summarizer
    ```

2. **Create a virtual environment:**

    ```bash
    python -m venv venv
    ```

3. **Activate the virtual environment:**

    - On Windows:

      ```bash
      venv\Scripts\activate
      ```

    - On macOS and Linux:

      ```bash
      source venv/bin/activate
      ```

4. **Install the required packages:**

    ```bash
    pip install -r requirements.txt
    ```

5. **Set up environment variables:**

   Create a `.env` file in the root directory of your project and add your Google Gemini API key:

    ```plaintext
    GOOGLE_API_KEY=your_google_gemini_api_key
    ```

## Usage

To run the app, execute the following command in your terminal:

```bash
streamlit run app.py
```

Follow these steps to use the application:

1. **Enter YouTube Video Link:** Paste the YouTube video link in the input field.
2. **Extract Transcript:** Click on "Get Detailed Notes" to extract the video transcript.
3. **Generate Summary:** The app will display a summarized version of the video content in bullet points.

## Code Overview

Here's a brief explanation of the key components of the code:

### 1. Environment Setup

```python
import streamlit as st
from dotenv import load_dotenv
import os
import google.generativeai as genai
from youtube_transcript_api import YouTubeTranscriptApi

load_dotenv()  # Load all the environment variables
```

- **Streamlit**: Used to create the web interface.
- **dotenv**: Loads environment variables from a `.env` file.
- **os**: Provides a way to access environment variables.
- **google.generativeai**: Allows interaction with Google Gemini's Generative AI.
- **YouTubeTranscriptApi**: Retrieves YouTube video transcripts.

### 2. Configuration and Initialization

```python
genai.configure(api_key=os.getenv("GOOGLE_API_KEY"))
```

- Configures the Google Gemini API using the API key stored in the environment variables.

### 3. Transcript Extraction

```python
def extract_transcript_details(youtube_video_url):
    try:
        video_id = youtube_video_url.split("=")[1]
        transcript_text = YouTubeTranscriptApi.get_transcript(video_id)

        transcript = ""
        for i in transcript_text:
            transcript += " " + i["text"]

        return transcript

    except Exception as e:
        raise e
```

- Extracts the video ID from the provided YouTube URL.
- Retrieves the transcript using the YouTube Transcript API.
- Concatenates transcript segments into a single string.

### 4. Summary Generation

```python
prompt = """You are a YouTube video summarizer. You will be taking the transcript text
and summarizing the entire video, providing the important summary in points
within 250 words. Please provide the summary of the text given here:  """

def generate_gemini_content(transcript_text, prompt):
    model = genai.GenerativeModel("gemini-pro")
    response = model.generate_content(prompt + transcript_text)
    return response.text
```

- Defines a prompt to instruct the Gemini model on how to summarize the video content.
- Generates a summary using Google Gemini's Generative AI.

### 5. User Interface

```python
custom_css = """
    <style>
        body {
            background-color: #f9f9f9;
            color: #333;
        }
        .stTextInput>div>div>div {
            background-color: #fff;
            border-radius: 10px;
            padding: 10px;
        }
        .stButton>button {
            background-color: #4CAF50;
            color: white;
            border-radius: 5px;
            padding: 10px 20px;
            margin-top: 10px;
            font-weight: bold;
            cursor: pointer;
        }
        .stMarkdown>div {
            background-color: #fff;
            border-radius: 10px;
            padding: 20px;
            margin-top: 20px;
        }
    </style>
"""

st.markdown(custom_css, unsafe_allow_html=True)

st.title("YOUTUBE VIDEO ABSTRACT")
youtube_link = st.text_input("Enter YouTube Video Link:")

if youtube_link:
    video_id = youtube_link.split("=")[1]
    st.image(f"http://img.youtube.com/vi/{video_id}/0.jpg", use_column_width=True)

if st.button("Get Detailed Notes"):
    transcript_text = extract_transcript_details(youtube_link)

    if transcript_text:
        summary = generate_gemini_content(transcript_text, prompt)
        st.markdown("## Detailed Notes:")
        st.write(summary)
```

- **Custom CSS**: Applies custom styling to the Streamlit components for a better user experience.
- **Input Field**: Collects the YouTube video link from the user.
- **Button**: Initiates the transcript extraction and summary generation process.
- **Display**: Shows the video's thumbnail and the generated summary.

