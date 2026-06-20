# Translator-language-translator-tool
Translation tool the user enter the text, Select a language source and a translate a required target language. It use the translator api google translate or a microsoft translate we also add the copy or a text to speech feature for better usability.
######### T H E F I N A L F O R G I T H U B U P L O D E ###########################################################
# 1. Install necessary libraries (Run this line once)
!pip install googletrans==4.0.0-rc1 gTTS

# 2. Imports
from googletrans import Translator
import ipywidgets as widgets
from IPython.display import display, Audio
from gtts import gTTS
import os

# 3. Initialize engine
translator = Translator()

# 4. UI Layout Components
text_input = widgets.Textarea(
    placeholder="Enter text to translate...",
    layout=widgets.Layout(width="100%", height="100px")
)

# Common Language List for selection
languages = [
    ("Auto-Detect", "auto"), ("English", "en"), ("Spanish", "es"),
    ("French", "fr"), ("German", "de"), ("Hindi", "hi"), ("Chinese", "zh-cn"),
    ("Marathi", "mr"), ("Kannada", "kn"), ("Bhojpuri (Bihari)", "bho"), ("Nepali", "ne")
]

# Source and Target Language Pickers
source_lang = widgets.Dropdown(options=languages, value="auto", description="Source:")
target_lang = widgets.Dropdown(options=languages[1:], value="es", description="Target:")

translate_button = widgets.Button(description="Translate", button_style="success")
speak_button = widgets.Button(description="🔊 Speak Translation", button_style="info")

output_box = widgets.Output()
audio_box = widgets.Output()

# Global variable to store last translation for TTS
last_translation = ""
last_target_code = ""

# 5. Core Translation Logic
def perform_translation(b):
    global last_translation, last_target_code
    output_box.clear_output()
    audio_box.clear_output()

    user_text = text_input.value.strip()
    if not user_text:
        with output_box: print("Please enter some text first.")
        return

    with output_box:
        print("Translating... Please wait.")
        try:
            # API Call
            result = translator.translate(user_text, src=source_lang.value, dest=target_lang.value)

            # Save variables for TTS usage
            last_translation = result.text
            last_target_code = target_lang.value

            # Clear "Translating..." message and show actual result
            output_box.clear_output()
            print(f"--- Translated Text ({target_lang.label}) ---")
            print(result.text)
        except Exception as e:
            output_box.clear_output()
            print("Error connecting to API. Please try again.")

# 6. Optional Feature: Text-To-Speech Logic
def play_audio(b):
    audio_box.clear_output()
    if not last_translation:
        with audio_box: print("Translate something first to hear it!")
        return

    with audio_box:
        try:
            # Generate speech file from translation
            tts = gTTS(text=last_translation, lang=last_target_code, slow=False)
            tts.save("speech.mp3")
            # Render audio player widget
            display(Audio("speech.mp3", autoplay=True))
        except Exception as e:
            print("Audio feature not supported for this specific dialect.")

# 7. Wire Events and Display Application
translate_button.on_click(perform_translation)
speak_button.on_click(play_audio)

print("=== TASK 1: LANGUAGE TRANSLATION TOOL ===")
display(text_input, source_lang, target_lang, translate_button, output_box, speak_button, audio_box)
