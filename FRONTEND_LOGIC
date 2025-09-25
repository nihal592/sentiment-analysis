// --- CONFIGURATION ---
const API_URL = 'http://127.0.0.1:5000/api';

// --- DOM Element References ---
const loginView=document.getElementById("loginView"),analyzerView=document.getElementById("analyzerView"),loginForm=document.getElementById("loginForm"),signupForm=document.getElementById("signupForm"),forgotPasswordForm=document.getElementById("forgotPasswordForm"),loginUsernameInput=document.getElementById("loginUsername"),loginPasswordInput=document.getElementById("loginPassword"),loginBtn=document.getElementById("loginBtn"),loginError=document.getElementById("loginError"),signupUsernameInput=document.getElementById("signupUsername"),signupPasswordInput=document.getElementById("signupPassword"),signupConfirmPasswordInput=document.getElementById("signupConfirmPassword"),signupBtn=document.getElementById("signupBtn"),signupError=document.getElementById("signupError"),signupSuccess=document.getElementById("signupSuccess"),resetEmailInput=document.getElementById("resetEmail"),resetBtn=document.getElementById("resetBtn"),resetSuccess=document.getElementById("resetSuccess"),showSignup=document.getElementById("showSignup"),showLoginFromSignup=document.getElementById("showLoginFromSignup"),showForgotPassword=document.getElementById("showForgotPassword"),showLoginFromForgot=document.getElementById("showLoginFromForgot"),logoutBtn=document.getElementById("logoutBtn"),commentInput=document.getElementById("commentInput"),analyzeBtn=document.getElementById("analyzeBtn"),resultsCard=document.getElementById("resultsCard"),placeholderCard=document.getElementById("placeholderCard"),loader=document.getElementById("loader"),resultContent=document.getElementById("resultContent");
const startFaceBtn=document.getElementById("startFaceBtn"),faceVideo=document.getElementById("faceVideo"),faceModelStatus=document.getElementById("faceModelStatus"),facialResult=document.getElementById("facialResult"),detectedMood=document.getElementById("detectedMood"),moodSuggestion=document.getElementById("moodSuggestion"),suggestionText=document.getElementById("suggestionText");
const startSignBtn=document.getElementById("startSignBtn"),signVideo=document.getElementById("signVideo"),signModelStatus=document.getElementById("signModelStatus"),signResult=document.getElementById("signResult"),signResultText=document.getElementById("signResultText"),speakBtn=document.getElementById("speakBtn");
const chatFab=document.getElementById("chat-fab"),chatWindow=document.getElementById("chat-window"),closeChatBtn=document.getElementById("close-chat"),chatbox=document.getElementById("chatbox"),chatInput=document.getElementById("chat-input"),sendChatBtn=document.getElementById("send-chat-btn");

let faceDetectionInterval, signDetectionInterval;
let faceStream, signStream;

// --- UTILITY FUNCTIONS ---
const apiPost = async (endpoint, body) => {
    try {
        const response = await fetch(`${API_URL}/${endpoint}`, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(body),
        });
        if (!response.ok) {
            const errorData = await response.json();
            throw new Error(errorData.error || 'Something went wrong');
        }
        return response.json();
    } catch (error) {
        console.error(`API Error on ${endpoint}:`, error);
        throw error;
    }
};

// --- AUTHENTICATION LOGIC ---
const showForm = (formElement) => {
    loginForm.classList.add("hidden");
    signupForm.classList.add("hidden");
    forgotPasswordForm.classList.add("hidden");
    formElement.classList.remove("hidden");
};

showSignup.addEventListener("click", e => { e.preventDefault(); showForm(signupForm); });
showLoginFromSignup.addEventListener("click", e => { e.preventDefault(); showForm(loginForm); });
showForgotPassword.addEventListener("click", e => { e.preventDefault(); showForm(forgotPasswordForm); });
showLoginFromForgot.addEventListener("click", e => { e.preventDefault(); showForm(loginForm); });

signupBtn.addEventListener("click", async () => {
    const username = signupUsernameInput.value.trim();
    const password = signupPasswordInput.value;
    const confirmPassword = signupConfirmPasswordInput.value;
    signupError.classList.add("hidden");
    signupSuccess.classList.add("hidden");

    if (!username || !password) {
        signupError.textContent = "Username and password are required.";
        return signupError.classList.remove("hidden");
    }
    if (password !== confirmPassword) {
        signupError.textContent = "Passwords do not match.";
        return signupError.classList.remove("hidden");
    }
    
    try {
        const data = await apiPost('register', { username, password });
        signupSuccess.textContent = "Account created! Please log in.";
        signupSuccess.classList.remove("hidden");
        setTimeout(() => showForm(loginForm), 2000);
    } catch (error) {
        signupError.textContent = error.message;
        signupError.classList.remove("hidden");
    }
});

loginBtn.addEventListener("click", async () => {
    const username = loginUsernameInput.value.trim();
    const password = loginPasswordInput.value;
    loginError.classList.add("hidden");

    try {
        const data = await apiPost('login', { username, password });
        if (data.success) {
            loginView.classList.add("hidden");
            analyzerView.classList.remove("hidden");
            analyzerView.classList.add("flex");
            faceModelStatus.textContent = 'Backend Ready.';
            signModelStatus.textContent = 'Backend Ready.';
        }
    } catch (error) {
        loginError.textContent = error.message;
        loginError.classList.remove("hidden");
    }
});

logoutBtn.addEventListener("click", () => {
    analyzerView.classList.add("hidden");
    analyzerView.classList.remove("flex");
    loginView.classList.remove("hidden");
    loginUsernameInput.value = "";
    loginPasswordInput.value = "";
    stopFaceVideo();
    stopSignVideo();
});

resetBtn.addEventListener("click", () => {
    const email = resetEmailInput.value;
    resetSuccess.classList.add("hidden");
    if (email) {
        resetSuccess.textContent = `A reset link has been sent to ${email}.`;
        resetSuccess.classList.remove("hidden");
    }
});

// --- VIDEO STREAMING & ANALYSIS ---
function captureFrame(videoElement) {
    const canvas = document.createElement('canvas');
    canvas.width = videoElement.videoWidth;
    canvas.height = videoElement.videoHeight;
    canvas.getContext('2d').drawImage(videoElement, 0, 0);
    return canvas.toDataURL('image/jpeg');
}

// Facial Analysis
async function startFaceVideo() {
    try {
        faceStream = await navigator.mediaDevices.getUserMedia({ video: { width: 640, height: 480 } });
        faceVideo.srcObject = faceStream;
        startFaceBtn.innerHTML = '<i data-lucide="video-off" class="w-5 h-5"></i>Stop Camera';
        lucide.createIcons();
        faceDetectionInterval = setInterval(async () => {
            const image_data_url = captureFrame(faceVideo);
            try {
                const result = await apiPost('analyze_face', { image: image_data_url });
                if (result.mood) {
                    detectedMood.textContent = result.mood;
                    facialResult.classList.remove('hidden');
                    if (result.mood.toLowerCase() === 'happy') {
                        moodSuggestion.classList.remove('hidden');
                        suggestionText.textContent = result.suggestion;
                    } else {
                        moodSuggestion.classList.add('hidden');
                    }
                } else {
                     facialResult.classList.add('hidden');
                     moodSuggestion.classList.add('hidden');
                }
            } catch (error) {
                console.error("Face analysis API call failed", error);
                facialResult.classList.add('hidden');
                moodSuggestion.classList.add('hidden');
            }
        }, 1000); // Send a frame every second
    } catch (e) {
        console.error("Error accessing webcam:", e);
        faceModelStatus.textContent = "Webcam access denied.";
    }
}

function stopFaceVideo() {
    if (faceStream) {
        faceStream.getTracks().forEach(track => track.stop());
        faceVideo.srcObject = null;
    }
    clearInterval(faceDetectionInterval);
    startFaceBtn.innerHTML = '<i data-lucide="video" class="w-5 h-5"></i>Start Camera';
    facialResult.classList.add("hidden");
    moodSuggestion.classList.add("hidden");
    lucide.createIcons();
}

startFaceBtn.addEventListener("click", () => {
    faceVideo.srcObject ? stopFaceVideo() : startFaceVideo();
});

// Sign Language Analysis
async function startSignVideo() {
    try {
        signStream = await navigator.mediaDevices.getUserMedia({ video: { width: 640, height: 480 } });
        signVideo.srcObject = signStream;
        startSignBtn.innerHTML = '<i data-lucide="video-off" class="w-5 h-5"></i>Stop Detection';
        lucide.createIcons();
        signDetectionInterval = setInterval(async () => {
            const image_data_url = captureFrame(signVideo);
             try {
                const result = await apiPost('analyze_sign', { image: image_data_url });
                signResultText.textContent = result.sign || '...';
            } catch (error) {
                console.error("Sign analysis API call failed", error);
                signResultText.textContent = '...';
            }
        }, 1000); // Send a frame every second
    } catch (e) {
        console.error("Error accessing webcam:", e);
        signModelStatus.textContent = "Webcam access denied.";
    }
}

function stopSignVideo() {
    if (signStream) {
        signStream.getTracks().forEach(track => track.stop());
        signVideo.srcObject = null;
    }
    clearInterval(signDetectionInterval);
    startSignBtn.innerHTML = '<i data-lucide="video" class="w-5 h-5"></i>Start Sign Detection';
    signResultText.textContent = "...";
    lucide.createIcons();
}

startSignBtn.addEventListener("click", () => {
    signVideo.srcObject ? stopSignVideo() : startSignVideo();
});

speakBtn.addEventListener("click", () => {
    const text = signResultText.textContent;
    if (text && text !== "...") {
        window.speechSynthesis.speak(new SpeechSynthesisUtterance(text));
    }
});

// --- CHATBOT LOGIC ---
chatFab.addEventListener("click",()=>{chatWindow.classList.toggle("hidden"),chatWindow.classList.toggle("flex")}),closeChatBtn.addEventListener("click",()=>{chatWindow.classList.add("hidden"),chatWindow.classList.remove("flex")});const handleUserMessage=()=>{const e=chatInput.value.trim();e&&(addMessageToChatbox(e,"user"),chatInput.value="",setTimeout(()=>{addMessageToChatbox(getBotResponse(e),"bot")},1e3))};sendChatBtn.addEventListener("click",handleUserMessage),chatInput.addEventListener("keydown",e=>{e.key==="Enter"&&handleUserMessage()});function addMessageToChatbox(e,t){const n=document.createElement("div"),o=document.createElement("div");n.classList.add("flex"),o.textContent=e,o.classList.add("p-3","rounded-lg","max-w-xs"),"user"===t?(n.classList.add("justify-end"),o.classList.add("bg-sky-600","text-white")):(n.classList.add("justify-start"),o.classList.add("bg-gray-700","text-white")),n.appendChild(o),chatbox.appendChild(n),chatbox.scrollTop=chatbox.scrollHeight}
function getBotResponse(e){const t=e.toLowerCase();return t.includes("hello")||t.includes("hi")?"Hello there! How can I assist you?":t.includes("help")?"You can analyze text sentiment, detect facial expressions, or interpret sign language via the backend.":t.includes("sentiment")?"Sentiment analysis determines if text is positive, negative, or neutral.":t.includes("facial")?"The facial analysis feature uses your camera to detect moods like happy, sad, or neutral.":t.includes("sign language")?"The sign language feature can recognize simple gestures and convert them to speech.":t.includes("how are you")?"I'm an AI, functioning perfectly. Thanks for asking!":"I'm not sure how to answer that."}


// --- TEXT SENTIMENT ANALYSIS LOGIC ---
const analyzeSentiment = async () => {
    const text = commentInput.value;
    if (text.trim() === '') {
        commentInput.classList.add("border-red-500");
        commentInput.placeholder = "Please enter a comment first!";
        return;
    }
    resultsCard.classList.remove("hidden");
    placeholderCard.classList.add("hidden");
    resultContent.innerHTML = "";
    loader.classList.remove("hidden");

    try {
        const data = await apiPost('analyze_text', { text });
        renderTextResults(data.sentiment, data.css_class, data.icon_html, data.score, data.positive_words, data.negative_words);
    } catch (error) {
        resultContent.innerHTML = `<p class="text-red-400 text-center">${error.message}</p>`;
    } finally {
        loader.classList.add("hidden");
    }
};

function renderTextResults(e,t,n,o,i,s){resultContent.innerHTML=`
                <div class="text-center p-6 rounded-lg transition-all duration-300 ${t}">
                    <div class="mx-auto mb-2">${n}</div>
                    <p class="text-2xl font-bold">${e}</p>
                    <p class="text-sm text-gray-400">Score: ${o.toFixed(3)}</p>
                </div>
                <div>
                    <div class="mb-4 mt-4">
                        <h3 class="font-semibold mb-2 flex items-center gap-2 text-green-400"><i data-lucide="thumbs-up" class="w-5 h-5"></i>Positive Keywords</h3>
                        <div class="flex flex-wrap gap-2">${renderKeywords(i,"bg-green-500/20 text-green-300")}</div>
                    </div>
                    <div>
                        <h3 class="font-semibold mb-2 flex items-center gap-2 text-red-400"><i data-lucide="thumbs-down" class="w-5 h-5"></i>Negative Keywords</h3>
                        <div class="flex flex-wrap gap-2">${renderKeywords(s,"bg-red-500/20 text-red-300")}</div>
                    </div>
                </div>
            `,lucide.createIcons()}
function renderKeywords(e,t){return 0===e.length?'<span class="text-gray-500 text-sm">None detected.</span>':e.map(e=>`<span class="px-3 py-1 text-sm rounded-full ${t}">${e}</span>`).join("")}
analyzeBtn.addEventListener("click", analyzeSentiment);

// --- Initial Icon Rendering ---
lucide.createIcons();
