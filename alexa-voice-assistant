import speech_recognition as sr
import pyttsx3
import pywhatkit
import wikipedia
import pyjokes
import datetime
import sounddevice as sd
import numpy as np
import io
import wave

# Initialize the recognizer and text-to-speech engine
listener = sr.Recognizer()
engine = pyttsx3.init()

# Set female voice (optional)
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[1].id)  # Use [0] for male voice

SAMPLE_RATE = 16000
DURATION = 5  # seconds to listen


def talk(text):
    """Convert text to speech."""
    print("Alexa:", text)
    engine.say(text)
    engine.runAndWait()


def record_audio():
    """Record audio using sounddevice and return an AudioData object."""
    print("Listening...")
    audio_np = sd.rec(
        int(DURATION * SAMPLE_RATE),
        samplerate=SAMPLE_RATE,
        channels=1,
        dtype='int16'
    )
    sd.wait()  # Wait until recording is complete

    # Convert numpy array to WAV bytes
    wav_buffer = io.BytesIO()
    with wave.open(wav_buffer, 'wb') as wf:
        wf.setnchannels(1)
        wf.setsampwidth(2)  # 16-bit audio = 2 bytes
        wf.setframerate(SAMPLE_RATE)
        wf.writeframes(audio_np.tobytes())
    wav_buffer.seek(0)

    with sr.AudioFile(wav_buffer) as source:
        audio_data = listener.record(source)
    return audio_data


def take_command():
    """Listen to user's voice and recognize the command."""
    command = ""
    try:
        audio_data = record_audio()
        command = listener.recognize_google(audio_data)
        command = command.lower()
        if 'alexa' in command:
            command = command.replace('alexa', '').strip()
        print("You said:", command)
    except sr.UnknownValueError:
        print("Sorry, I didn't understand that.")
    except sr.RequestError:
        print("Network error. Check your internet connection.")
    except Exception as e:
        print(f"Error: {e}")
    return command


def run_alexa():
    """Process the command and respond accordingly."""
    command = take_command()

    if 'play' in command:
        song = command.replace('play', '').strip()
        talk("Playing " + song)
        pywhatkit.playonyt(song)

    elif 'time' in command:
        time = datetime.datetime.now().strftime('%I:%M %p')
        talk("The current time is " + time)

    elif 'who is' in command or 'who the heck is' in command:
        person = command.replace('who the heck is', '').replace('who is', '').strip()
        try:
            info = wikipedia.summary(person, 1)
            print(info)
            talk(info)
        except Exception:
            talk("I couldn't find information on that.")

    elif 'date' in command:
        talk("Sorry, I have a headache today.")

    elif 'are you single' in command:
        talk("I am in a relationship with Wi-Fi.")

    elif 'joke' in command:
        talk(pyjokes.get_joke())

    elif command != "":
        talk("Please say the command again.")

    else:
        pass  # No voice detected, ignore silently


# Run Alexa continuously
if __name__ == "__main__":
    print("Alexa is running! Say 'Alexa' followed by a command.")
    print("Press Ctrl+C to stop.\n")
    while True:
        run_alexa()
