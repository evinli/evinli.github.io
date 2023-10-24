---
title: "EduSphere"
summary: "An AR language learning game submitted for Hack the North 2023, selected as one of twelve finalist projects."
tags: ["REST APIs", "Flask", "Google Cloud Translation API", "Cohere LLM API"]
---

### What Is It
EduSphere is an interactive AR/VR language learning VisionOS application designed for the new Apple Vision Pro. It contains three fully developed features: a 3D popup game, a multi-lingual chatbot, and an immersive learning environment. This was our project submission for the Hack the North 2023 at UWaterloo, where we were selected as a finalist.

{{< youtube r9khaSJEWeY >}}

### How We Built It
We built the VisionOS app using the Beta development kit for the Apple Vision Pro. The front-end and AR/VR components were made using Swift, SwiftUI, Alamofire, RealityKit, and concurrent MVVM design architecture. We stored our 3D models in Google Cloud Bucket Storage, with their corresponding metadata on CockroachDB.

Our back-end features various scripts involving Python, SQL, Cohere LLMs, and Google Translation AI to allow for real-time text translation and conversing. I used the `googletrans` python library to translate text and generated chatbot responses to user prompts using the Cohere API. To connect our front-end with the back-end and allow HTTP `get` and `post` requests, we developed REST APIs in Flask to access each of our endpoints.

This was my first time working with Flask and building APIs so I found it super cool and also a lot easier than I'd imagined! Here are some of the APIs I created:

```python
# Example: http://127.0.0.1:5000/translate?word=hello&src=english&dest=chinese%20(simplified)
@app.route('/translate', methods=['GET'])
def get_translation():
    word = request.args.get('word')
    src = request.args.get('src')
    dest = request.args.get('dest')
    translation = translate.get_translation(word, src, dest)
    jsonOutput = {"translation": translation}
    return jsonOutput

# Example: http://127.0.0.1:5000/languages
@app.route('/languages', methods=['GET'])
def get_languages():
    return jsonify(translate.get_all_languages())

# Example: http://127.0.0.1:5000/chatbot-query?msg=have%20a%20conversation%20with%20me%20in%20french,%20and%20subtely%20give%20me%20feedback%20on%20my%20responses
@app.route('/chatbot-query', methods=['GET'])
def chatbot_query():
    msg = request.args.get('msg')
    ans = chatbot.get_response(msg, convo_history)

    # Add message and answer to the chat history
    user_message = {"user_name": "User", "text": msg}
    bot_message = {"user_name": "Chatbot", "text": ans}
    convo_history.append(user_message)
    convo_history.append(bot_message)

    jsonOutput = {"reply": ans}
    return jsonify(jsonOutput)
```

{{< alert "github" >}}
You can find our source code [here](https://github.com/evinli/EduSphere-VisionOS) and our DevPost [here](https://devpost.com/software/edusphere-95goxk).
{{< /alert >}}