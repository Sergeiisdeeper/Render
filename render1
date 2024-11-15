from fastapi import FastAPI, WebSocket
from fastapi.responses import HTMLResponse
import openai
import os

# Initialize FastAPI app
app = FastAPI()

# GPT-4 API Key from environment variable
openai.api_key = os.getenv("OPENAI_API_KEY")

# Serve HTML Page
@app.get("/")
async def homepage():
    html_content = """
    <!DOCTYPE html>
    <html>
    <head>
        <title>My GPT Chat</title>
        <link rel="stylesheet" href="/static/style.css">
    </head>
    <body>
        <h1>Welcome to My GPT Chat</h1>
        <p>Chat in real-time with GPT-4 below!</p>
        <div id="chatbox">
            <div id="messages"></div>
            <input id="inputMessage" type="text" placeholder="Type your message here..." />
            <button onclick="sendMessage()">Send</button>
        </div>
        <script>
            const ws = new WebSocket("ws://" + window.location.host + "/ws");
            ws.onmessage = (event) => {
                const messagesDiv = document.getElementById("messages");
                messagesDiv.innerHTML += `<p>${event.data}</p>`;
            };
            const sendMessage = () => {
                const input = document.getElementById("inputMessage");
                ws.send(input.value);
                input.value = "";
            };
        </script>
    </body>
    </html>
    """
    return HTMLResponse(content=html_content)

# WebSocket for Chat
@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    while True:
        user_message = await websocket.receive_text()
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[{"role": "user", "content": user_message}],
        )
        reply = response.choices[0].message.content
        await websocket.send_text(reply)

