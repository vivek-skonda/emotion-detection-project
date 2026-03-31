import requests
import json

def emotion_detector(text_to_analyse):
    url = 'https://sn-watson-sentiment-bert.labs.skills.network/v1/watson.runtime.nlp.v1/NlpService/SentimentPredict'
    header = {"grpc-metadata-mm-model-id": "emotion_aggregated-workflow_lang_en_stock"}
    myobj = {"raw_document": {"text": text_to_analyse}}

    response = requests.post(url, json=myobj, headers=header)

    # Handle blank input → API returns 400
    if response.status_code == 400:
        return {
            "anger": None,
            "disgust": None,
            "fear": None,
            "joy": None,
            "sadness": None,
            "dominant_emotion": None
        }

    formatted_response = json.loads(response.text)

    anger = formatted_response["emotionPredictions"][0]["emotion"]["anger"]
    disgust = formatted_response["emotionPredictions"][0]["emotion"]["disgust"]
    fear = formatted_response["emotionPredictions"][0]["emotion"]["fear"]
    joy = formatted_response["emotionPredictions"][0]["emotion"]["joy"]
    sadness = formatted_response["emotionPredictions"][0]["emotion"]["sadness"]

    # Determine dominant emotion
    emotion_scores = {
        "anger": anger,
        "disgust": disgust,
        "fear": fear,
        "joy": joy,
        "sadness": sadness
    }
    dominant_emotion = max(emotion_scores, key=emotion_scores.get)

    return {
        "anger": anger,
        "disgust": disgust,
        "fear": fear,
        "joy": joy,
        "sadness": sadness,
        "dominant_emotion": dominant_emotion
    }