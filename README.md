## AIM

To design and develop an AI-powered Youth Wellness App that assists young individuals in maintaining mental and emotional well-being by offering mood tracking, AI-based sentiment analysis, wellness recommendations, journaling, and chatbot support.

The app aims to:

Detect early signs of stress, anxiety, or low mood.

Provide personalized wellness tips (sleep hygiene, exercise, meditation, gratitude journaling).

Serve as a 24/7 AI companion for motivation and guidance.

Encourage self-awareness through daily mood tracking and activity logs.

## PROCEDURE
1. Requirement Analysis

Collect information on youth mental health challenges.

Decide on core features:

User authentication

Mood tracking (rating + text notes)

AI chatbot (for emotional support)

Sentiment analysis engine

Wellness suggestions (quotes, exercises, journaling prompts)

Define success criteria: engaging UI, low latency, privacy-focused.

2. System Design

Frontend: Mobile-first design with clean UI.

Backend: REST API for handling authentication, mood records, and AI analysis.

Database: Stores user profiles, daily moods, and recommendations.

AI Layer: Sentiment analysis using NLP to interpret emotions.

## ER Diagram (Simplified)

User (UserID, Name, Age, Email, Password)
    |
    |---< MoodLog (LogID, UserID, MoodRating, Notes, Date)
    |
    |---< Recommendation (RecID, UserID, Text, Date)

3. Technology Stack

Frontend: React Native / Flutter (cross-platform mobile app).

Backend: Flask (Python) or Node.js.

Database: MySQL / Firebase.

AI Module: Python (NLTK, TextBlob, or Transformers for NLP).

Deployment: Heroku / AWS Cloud.

4. Implementation Steps

Build authentication system (sign up, login, logout).

Create Mood Tracking Module (daily rating + optional text notes).

Connect database to store mood logs.

Implement AI Sentiment Analyzer to process user text.

Develop Recommendation Engine (motivational quotes, mindfulness activities).

Add Chatbot module for conversational support.

Design dashboards to display weekly/monthly mood trends.

Deploy app on a server and test with sample users.

5. Testing

Unit Testing: Check login, data saving, sentiment analysis separately.

Integration Testing: Verify end-to-end flow (mood input → analysis → suggestion).

User Acceptance Testing (UAT): Feedback from 10–20 students.

6. Deployment

Deploy backend API on Heroku/AWS.

Publish mobile app on Google Play Store (for Android) or TestFlight/App Store (for iOS).

ALGORITHM

Start application.

User registers or logs in.

User enters mood rating (1–5) and an optional journal note.

Save mood entry into the database.

Sentiment analysis:

If negative mood detected → Show motivational quote + suggest meditation/relaxation.

If neutral mood → Suggest journaling or short break.

If positive mood → Encourage gratitude journaling or exercise.

Provide daily wellness tips + access to chatbot.

Update dashboard with weekly progress.

End.

## PROGRAM (Python Flask Backend + Sentiment Analysis)
# backend.py

from flask import Flask, request, jsonify
from textblob import TextBlob
from datetime import datetime

app = Flask(__name__)

# Simulated in-memory storage (replace with DB in real app)
users = {}           # user_id -> { “email”: ..., “password_hash”: ... }
mood_logs = []       # list of { user_id, rating, notes, sentiment, suggestion, timestamp }

def analyze_sentiment(text):
    if not text:
        return 0.0
    sentiment = TextBlob(text).sentiment.polarity
    return sentiment

def generate_suggestion(rating, sentiment):
    # rating: say 1 (very bad) to 5 (very good)
    # sentiment: negative (<0), neutral (~0), positive (>0)

    if rating <= 2 or sentiment < -0.2:
        return "We understand you're going through a tough time. Try mindfulness or talking with someone you trust."
    elif rating == 3:
        return "Feeling ok? Maybe journaling or short walk might help."
    else:  # rating 4-5 and positive sentiment
        return "That’s great! Keep up the good vibes — gratitude journaling or some fun activity will go well."

@app.route('/register', methods=['POST'])
def register_user():
    data = request.get_json()
    email = data.get('email')
    password = data.get('password')
    # do validation: email format, password strength, existing user etc.
    user_id = len(users) + 1
    users[user_id] = {
        "email": email,
        "password_hash": password  # in real world, hash this
    }
    return jsonify({"status": "ok", "user_id": user_id})

@app.route('/login', methods=['POST'])
def login_user():
    data = request.get_json()
    email = data.get('email')
    password = data.get('password')
    # find user
    for uid, u in users.items():
        if u['email'] == email and u['password_hash'] == password:
            return jsonify({"status": "ok", "user_id": uid})
    return jsonify({"status": "error", "message": "Invalid credentials"}), 401

@app.route('/log_mood', methods=['POST'])
def log_mood():
    data = request.get_json()
    user_id = data.get('user_id')
    rating = data.get('rating', 3)
    notes = data.get('notes', "")

    if user_id not in users:
        return jsonify({"status": "error", "message": "User not found"}), 404

    sentiment = analyze_sentiment(notes)
    suggestion = generate_suggestion(rating, sentiment)
    timestamp = datetime.utcnow().isoformat()

    log_entry = {
        "user_id": user_id,
        "rating": rating,
        "notes": notes,
        "sentiment": sentiment,
        "suggestion": suggestion,
        "timestamp": timestamp
    }
    mood_logs.append(log_entry)

    return jsonify({"status": "ok", "entry": log_entry})

@app.route('/history/<int:user_id>', methods=['GET'])
def get_history(user_id):
    if user_id not in users:
        return jsonify({"status": "error", "message": "User not found"}), 404

    user_history = [ entry for entry in mood_logs if entry['user_id'] == user_id ]
    return jsonify({"status": "ok", "history": user_history})

if __name__ == '__main__':
    app.run(debug=True)



 Explanation of Program:

/log_mood: Accepts mood rating + notes from the user, analyzes text using TextBlob, and returns suggestions.

/history: Returns all past mood logs for that user.

EXPECTED OUTPUT

When user logs a sad mood:

{
  "user": "Karthik",
  "rating": 2,
  "notes": "Feeling very stressed about exams",
  "sentiment": -0.5,
  "suggestion": "It seems you're feeling low. Try deep breathing or talking to a close friend.",
  "date": "2025-09-21 22:30"
}


When user logs a happy mood:

{
  "user": "Karthik",
  "rating": 5,
  "notes": "Had a great day with friends",
  "sentiment": 0.6,
  "suggestion": "You're in a positive mood! Keep it up with gratitude journaling.",
  "date": "2025-09-21 22:35"
}
