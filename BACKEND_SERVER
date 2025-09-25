from flask import Flask, request, jsonify
from flask_cors import CORS
import re
import random

# Initialize Flask App
app = Flask(__name__)
# Enable Cross-Origin Resource Sharing
CORS(app)

# --- In-memory database for users (for demonstration purposes) ---
users = {}

# --- Helper data and functions ---
positive_words=["good","great","excellent","helpful","amazing","awesome","satisfied","clear","thanks","thank","happy","pleased","wonderful","professional","caring","understanding","effective","quick","fast","efficient","recommend","positive","love","best","friendly","knowledgeable","polite","comforting"]
negative_words=["bad","poor","terrible","awful","disappointed","unhelpful","confusing","slow","late","wait","waiting","frustrating","problem","issue","worst","never","difficult","unprofessional","ignored","pain","negative","hate","useless","rude","dismissive","long","unclear"]
happy_suggestions = ["Listen to your favorite upbeat music!", "Watch a comedy movie or show.", "Share your good mood with a friend.", "Go for a walk and enjoy the outdoors."]

# --- API Endpoints ---

@app.route('/api/register', methods=['POST'])
def register():
    data = request.get_json()
    username = data.get('username')
    password = data.get('password')

    if not username or not password:
        return jsonify({"error": "Username and password are required"}), 400
    
    if username in users:
        return jsonify({"error": "Username already exists"}), 400

    users[username] = {"password": password}
    print(f"User Registered: {username}") # For debugging
    return jsonify({"success": True, "message": "User registered successfully"}), 201

@app.route('/api/login', methods=['POST'])
def login():
    data = request.get_json()
    username = data.get('username')
    password = data.get('password')

    user = users.get(username)
    if user and user['password'] == password:
        print(f"User Logged In: {username}") # For debugging
        return jsonify({"success": True, "message": "Login successful"})
    
    return jsonify({"error": "Invalid username or password"}), 401

@app.route('/api/analyze_text', methods=['POST'])
def analyze_text():
    data = request.get_json()
    text = data.get('text', '').lower()
    
    # Simple word-based sentiment analysis
    words = re.sub(r'[.,!?;:"\']', '', text).split()
    score = 0
    pos_words_found = []
    neg_words_found = []

    for word in words:
        if word in positive_words:
            score += 1
            if word not in pos_words_found: pos_words_found.append(word)
        elif word in negative_words:
            score -= 1
            if word not in neg_words_found: neg_words_found.append(word)

    normalized_score = score / len(words) if words else 0
    
    if normalized_score > 0.05:
        sentiment = "Positive"
        css_class = "bg-green-500/10"
        icon_html = '<i data-lucide="smile" class="w-16 h-16 mx-auto text-green-400"></i>'
    elif normalized_score < -0.05:
        sentiment = "Negative"
        css_class = "bg-red-500/10"
        icon_html = '<i data-lucide="frown" class="w-16 h-16 mx-auto text-red-400"></i>'
    else:
        sentiment = "Neutral"
        css_class = "bg-gray-500/10"
        icon_html = '<i data-lucide="meh" class="w-16 h-16 mx-auto text-gray-400"></i>'

    return jsonify({
        "sentiment": sentiment,
        "score": normalized_score,
        "positive_words": pos_words_found,
        "negative_words": neg_words_found,
        "css_class": css_class,
        "icon_html": icon_html
    })

@app.route('/api/analyze_face', methods=['POST'])
def analyze_face():
    # This is a MOCKED endpoint. A real implementation would require
    # libraries like OpenCV and a computer vision model.
    # We will simulate the analysis.
    moods = ["Happy", "Sad", "Neutral", "Surprised"]
    detected_mood = random.choice(moods)
    
    response = {"mood": detected_mood}
    if detected_mood == "Happy":
        response["suggestion"] = random.choice(happy_suggestions)

    return jsonify(response)


@app.route('/api/analyze_sign', methods=['POST'])
def analyze_sign():
    # This is a MOCKED endpoint for demonstration.
    # A real implementation is highly complex.
    signs = ["Hello", "Victory", "Thumbs Up", "..."]
    detected_sign = random.choice(signs)
    return jsonify({"sign": detected_sign})


if __name__ == '__main__':
    app.run(debug=True)

