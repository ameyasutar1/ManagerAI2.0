

  <h1>ManagerAI 2.0</h1>

  <h2>Description</h2>
  <p>Manager.AI is an autonomous meeting assistant—“John Doe”—built with LiveKit and Google Gemini Live API, augmented by a custom RAG system. It joins your LiveKit room, greets participants, retrieves and stores context, and concludes with a concise recap. Self‑contained Python script.</p>

  <hr>

  <h2>📋 Prerequisites</h2>
  <ul>
    <li>Python 3.12+</li>
    <li>LiveKit server (URL, API key, API secret)</li>
    <li>Google Cloud credentials for Gemini LLM and STT (<code>GOOGLE_API_KEY</code>, <code>GOOGLE_APPLICATION_CREDENTIALS</code>)</li>
    <li><code>RAGSystem</code> implementation in <code>rag.py</code></li>
  </ul>
  <p>Store in <code>.env.local</code>:</p>
  <pre><code>LIVEKIT_URL=wss://your-livekit-server
LIVEKIT_API_KEY=xxxx
LIVEKIT_API_SECRET=yyyy
GOOGLE_API_KEY=zzzz
GOOGLE_APPLICATION_CREDENTIALS=/path/to/google-credentials.json
</code></pre>

  <hr>

  <h2>🚀 Quickstart</h2>

  <h3>Install dependencies</h3>
  <pre><code>pip install livekit-agents livekit-plugins-google python-dotenv aiohttp
# plus your RAGSystem requirements
</code></pre>

  <h3>Run the agent</h3>
  <pre><code>python agent.py dev</code></pre>
  <blockquote>Join the LiveKit room via any LiveKit client; the agent appears as “John Doe” and greets you automatically.</blockquote>

  <hr>

  <h2>🧩 How It Works</h2>
  <ol>
    <li>
      <strong>Configuration &amp; Logging</strong><br>
      Loads <code>.env.local</code> and uses Python <code>logging</code> (INFO level).
    </li>
    <li>
      <strong>RAG Initialization</strong>
      <pre><code>rag = RAGSystem(collection_name="my_documents")
logger.info("RAGSystem initialized")</code></pre>
      <p>Connects to your vector store for context retrieval and storage.</p>
    </li>
    <li>
      <strong>Function Tools</strong>
      <ul>
        <li><strong>SendMessage</strong>: broadcast/chat.</li>
        <li><strong>ListParticipants</strong>: list current attendees.</li>
        <li><strong>QueryRAG</strong>: fetch relevant history.</li>
        <li><strong>AddDataToDB</strong>: store new insights.</li>
        <li><strong>CloseMeeting</strong>: end the session.</li>
      </ul>
    </li>
    <li>
      <strong>Entrypoint</strong>
      <pre><code>async def entrypoint(ctx: JobContext):
    await ctx.connect(auto_subscribe=AutoSubscribe.AUDIO_ONLY)
    fnc_ctx = AssistantFnc(ctx.room)
    participant = await ctx.wait_for_participant()
    await run_multimodal_agent(ctx, participant, fnc_ctx)</code></pre>
      <p>Subscribes to audio and waits for participants.</p>
    </li>
    <li>
      <strong>run_multimodal_agent</strong>
      <pre><code>model = google.beta.realtime.RealtimeModel(
    model="gemini-2.0-flash-exp",
    voice="Charon",
    temperature=0.3,
    instructions="…John Doe persona & behaviors…",
    modalities=["AUDIO"],
)
agent = MultimodalAgent(
    model=model,
    chat_ctx=llm.ChatContext(),
    fnc_ctx=fnc_ctx
)
agent.start(ctx.room, participant)
agent.generate_reply()</code></pre>
      <p>Uses Gemini Live API for LLM responses in “Charon” voice, with the embedded persona.</p>
    </li>
  </ol>

  <hr>

  <h2>🔧 Customization</h2>
  <ul>
    <li><strong>Persona &amp; Instructions</strong>: edit the <code>instructions</code> string in <code>RealtimeModel</code>.</li>
    <li><strong>RAG Settings</strong>: adjust <code>n_results</code>, metadata, sorting in <code>QueryRAG</code>.</li>
    <li><strong>Logging</strong>: change <code>logging.basicConfig(level=…)</code>.</li>
  </ul>

  <hr>

  <h2>⚙️ Future Enhancements</h2>
  <ul>
    <li>Summarization via RAG + LLM in <code>CloseMeeting</code>.</li>
    <li>Action‑item tracking, export to JSON/CSV.</li>
    <li>REST API for external control or monitoring.</li>
  </ul>

  <p>Enjoy your AI‑powered, agentic meeting assistant!</p>

</body>
</html>
