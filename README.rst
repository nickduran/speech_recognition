Google Speech Recognition
=========================

.. image:: https://img.shields.io/pypi/dm/SpeechRecognition.svg
    :target: https://pypi.python.org/pypi/SpeechRecognition/
    :alt: Downloads

.. image:: https://img.shields.io/pypi/v/SpeechRecognition.svg
    :target: https://pypi.python.org/pypi/SpeechRecognition/
    :alt: Latest Version

.. image:: https://img.shields.io/pypi/status/SpeechRecognition.svg
    :target: https://pypi.python.org/pypi/SpeechRecognition/
    :alt: Development Status

.. image:: https://img.shields.io/pypi/pyversions/SpeechRecognition.svg
    :target: https://pypi.python.org/pypi/SpeechRecognition/
    :alt: Supported Python Versions

.. image:: https://img.shields.io/pypi/l/SpeechRecognition.svg
    :target: https://pypi.python.org/pypi/SpeechRecognition/
    :alt: License

Library for performing speech recognition with the Google Speech Recognition API.

Links:

-  `PyPI <https://pypi.python.org/pypi/SpeechRecognition/>`__
-  `GitHub <https://github.com/Uberi/speech_recognition>`__

Quickstart: ``pip install SpeechRecognition``. See the "Installing" section for more details.

To quickly try it out, run ``python -m speech_recognition`` after installing.

How to cite this library (APA style):

    Zhang, A. (2015). Speech Recognition (Version 2.0) [Software]. Available from https://github.com/Uberi/speech_recognition#readme.

How to cite this library (Chicago style):

    Zhang, Anthony. 2015. *Speech Recognition* (version 2.0).

Also check out the [Python Baidu Yuyin API](https://github.com/DelightRun/PyBaiduYuyin), which is based on this project.

Examples
--------

Recognize speech input from the microphone:

.. code:: python

                                                   # NOTE: this requires PyAudio because it uses the Microphone class
    import speech_recognition as sr
    r = sr.Recognizer()
    with sr.Microphone() as source:                # use the default microphone as the audio source
        audio = r.listen(source)                   # listen for the first phrase and extract it into audio data

    try:
        print("You said " + r.recognize(audio))    # recognize speech using Google Speech Recognition
    except LookupError:                            # speech is unintelligible
        print("Could not understand audio")

Transcribe a WAV audio file:

.. code:: python

    import speech_recognition as sr
    r = sr.Recognizer()
    with sr.WavFile("test.wav") as source:              # use "test.wav" as the audio source
        audio = r.record(source)                        # extract audio data from the file

    try:
        print("Transcription: " + r.recognize(audio))   # recognize speech using Google Speech Recognition
    except LookupError:                                 # speech is unintelligible
        print("Could not understand audio")

Transcribe a WAV audio file and show the confidence of each possibility:

.. code:: python

    import speech_recognition as sr
    r = sr.Recognizer()
    with sr.WavFile("test.wav") as source:              # use "test.wav" as the audio source
        audio = r.record(source)                        # extract audio data from the file

    try:
        list = r.recognize(audio,True)                  # generate a list of possible transcriptions
        print("Possible transcriptions:")
        for prediction in list:
            print(" " + prediction["text"] + " (" + str(prediction["confidence"]*100) + "%)")
    except LookupError:                                 # speech is unintelligible
        print("Could not understand audio")

Calibrate the recognizer energy threshold (see ``recognizer_instance.energy_threshold``) for ambient noise levels:

.. code:: python

    import speech_recognition as sr
    r = sr.Recognizer()
    with sr.Microphone() as source:                # use the default microphone as the audio source
        r.adjust_for_ambient_noise(source)         # listen for 1 second to calibrate the energy threshold for ambient noise levels
        audio = r.listen(source)                   # now when we listen, the energy threshold is already set to a good value, and we can reliably catch speech right away

    try:
        print("You said " + r.recognize(audio))    # recognize speech using Google Speech Recognition
    except LookupError:                            # speech is unintelligible
        print("Could not understand audio")

Listening to a microphone in the background:

.. code:: python

    import speech_recognition as sr
    def callback(recognizer, audio):                          # this is called from the background thread
        try:
            print("You said " + recognizer.recognize(audio))  # received audio data, now need to recognize it
        except LookupError:
            print("Oops! Didn't catch that")
    r = sr.Recognizer()
    m = sr.Microphone()
    with m as source: r.adjust_for_ambient_noise(source)      # we only need to calibrate once, before we start listening
    stop_listening = r.listen_in_background(m, callback)
    
    import time
    for _ in range(50): time.sleep(0.1)                       # we're still listening even though the main thread is blocked - loop runs for about 5 seconds
    stop_listening()                                          # call the stop function to stop the background thread
    while True: time.sleep(0.1)                               # the background thread stops soon after we call the stop function

Installing
----------

First, make sure you have all the requirements, listed in the "Requirements" section.

The easiest way to install this is using ``pip install SpeechRecognition``.

Otherwise, download the source distribution from `PyPI <https://pypi.python.org/pypi/SpeechRecognition/>`__, and extract the archive.

In the folder, run ``python setup.py install``.

Requirements
------------

API Key
~~~~~~~

Google Speech Recognition API requires an API key. This library defaults to using one that was reverse engineered out of Chrome, but **it is not recommended that you use this API key for anything other than personal or testing purposes**.

Instead, it is best to obtain your own API key by following the steps on the `API Keys <http://www.chromium.org/developers/how-tos/api-keys>`__ page at the Chromium Developers site.

Python
~~~~~~

The first software requirement is `Python 2.6, 2.7, or Python 3.3+ <https://www.python.org/download/releases/>`__. This is required to use the library.

PyAudio (for microphone users)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you want to use audio input from microphones, `PyAudio <http://people.csail.mit.edu/hubert/pyaudio/#downloads>`__ is also necessary. If not installed, the library will still work, but ``Microphone`` will be undefined.

The official PyAudio builds seem to be broken on Windows. As a result, in the ``installers`` folder you will find `unofficial PyAudio builds for Windows <http://www.lfd.uci.edu/~gohlke/pythonlibs/#pyaudio>`__ that actually work. Run the installer corresponding to your Python version to install PyAudio.

On Debain-based distributions such as Ubuntu, you can generally install PyAudio by running ``sudo apt-get install python-pyaudio python3-pyaudio``, which will install it for both Python 2 and Python 3.

On other POSIX-based systems, simply use the packages provided on the downloads page linked above, or compile and install it from source.

FLAC (for some systems)
~~~~~~~~~~~~~~~~~~~~~~~

A FLAC encoder is required to encode the audio data to send to the API. If using Windows, OS X, or Linux on an i385-compatible architecture, the encoder is already bundled with this library.

Otherwise, ensure that you have the ``flac`` command line tool, which is often available through the system package manager.

In summary, this library requires:

* Python 2.6, 2.7, or 3.3+
* PyAudio (required only if you need to use microphone input)
* FLAC encoder (required only if the system is not x86-based Windows/Linux/OS X)

Troubleshooting
---------------

The ``Microphone`` class is missing/not defined!
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This class is not defined when PyAudio is not available.

Make sure you have PyAudio installed, and make sure you can import it correctly. Test this out by opening a Python console (make sure to use the same version you're running your program with!) and typing in ``import pyaudio``. If you get an error, PyAudio is not installed or not configured correctly.

See the "Requirements" section for more information about installing PyAudio.

The recognizer tries to recognize speech even when I'm not speaking.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Try increasing the ``recognizer_instance.energy_threshold`` property. This is basically how sensitive the recognizer is to when recognition should start. Higher values mean that it will be less sensitive, which is useful if you are in a loud room.

This value depends entirely on your microphone or audio data. There is no one-size-fits-all value, but good values typically range from 50 to 4000.

The recognizer can't recognize speech right after it starts listening for the first time.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``recognizer_instance.energy_threshold`` property is probably set to a value that is too high to start off with, and then being adjusted lower automatically by dynamic energy threshold adjustment. Before it is at a good level, the energy threshold is so high that speech is just considered ambient noise.

The solution is to decrease this threshold, or call ``recognizer_instance.adjust_for_ambient_noise(source, duration = 1)`` beforehand, which will set the threshold to a good value automatically.

The recognizer doesn't understand my particular language/dialect.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Try setting the language code when creating a ``Recognizer`` instance. For example, for British English it is better to use ``Recognizer("en-GB")`` rather than the default US English.

See the "Reference" section for more information about language codes.

The program doesn't run when compiled with `PyInstaller <https://github.com/pyinstaller/pyinstaller/wiki>`__.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

PyInstaller doesn't know that the FLAC converters need to be bundled with the application. To resolve this, we need to make a PyInstaller hook to include those files and tell PyInstaller where that hook is:

1. Create a folder in your project directory to store PyInstaller hooks, if the project doesn't already have one. For example, a folder ``pyinstaller-hooks`` in the project root directory.
2. Create a file called ``hook-speech_recognition.py`` in that folder, with the following contents:

    .. code:: python

        from PyInstaller.hooks.hookutils import collect_data_files
        datas = collect_data_files("speech_recognition")

3. When building the project using something like ``pyinstaller SOME_SCRIPT.py``, simply supply the ``--additional-hooks-dir`` option set to the PyInstaller hooks folder. For example, ``pyinstaller --additional-hooks-dir pyinstaller-hooks/ SOME_SCRIPT.py``.

On Ubuntu/Debian, I get errors like "jack server is not running or cannot be started" or "Cannot lock down [...] byte memory area (Cannot allocate memory)".
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Linux audio stack is pretty fickle. There are a few things that can cause these issues.

First, make sure JACK is installed - to install it, run `sudo apt-get install multimedia-jack`

You will then want to configure the JACK daemon correctly to avoid that "Cannot allocate memory" error. Run ``sudo dpkg-reconfigure -p high jackd2`` and select "Yes" to do so.

Now, you will want to make sure your current user is in the ``audio`` group. You can add your current user to this group by running ``sudo adduser $(whoami) audio``.

Unfortunately, these changes will require you to reboot before they take effect.

After rebooting, run ``pulseaudio --kill``, followed by ``jack_control start``, to fix the "jack server is not running or cannot be started" error.

On Ubuntu/Debian, I get annoying output in the terminal saying things like "bt_audio_service_open: [...] Connection refused" and various others.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The "bt_audio_service_open" error means that you have a Bluetooth audio device, but as a physical device is not currently connected, we can't actually use it - if you're not using a Bluetooth microphone, then this can be safely ignored. If you are, and audio isn't working, then double check to make sure your microphone is actually connected. There does not seem to be a simple way to disable these messages.

For errors of the form "ALSA lib [...] Unknown PCM", see `this StackOverflow answer <http://stackoverflow.com/questions/7088672/pyaudio-working-but-spits-out-error-messages-each-time>`__. Basically, to get rid of an error of the form "Unknown PCM cards.pcm.rear", simply comment out ``pcm.rear cards.pcm.rear`` in ``/usr/share/alsa/alsa.conf``, ``~/.asoundrc``, and ``/etc/asound.conf``.

Reference
---------

``Microphone(device_index = None)``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is available if PyAudio is available, and is undefined otherwise.

Creates a new ``Microphone`` instance, which represents a physical microphone on the computer. Subclass of ``AudioSource``.

If ``device_index`` is unspecified or ``None``, the default microphone is used as the audio source. Otherwise, ``device_index`` should be the index of the device to use for audio input.

A device index is an integer between 0 and ``pyaudio.get_device_count() - 1`` (assume we have used ``import pyaudio`` beforehand) inclusive. It represents an audio device such as a microphone or speaker. See the `PyAudio documentation <http://people.csail.mit.edu/hubert/pyaudio/docs/>`__ for more details.

This class is to be used with ``with`` statements:

.. code:: python

    with Microphone() as source:    # open the microphone and start recording
        pass                        # do things here - `source` is the Microphone instance created above
                                    # the microphone is automatically released at this point

``WavFile(filename_or_fileobject)``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Creates a new ``WavFile`` instance, which represents a WAV audio file. Subclass of ``AudioSource``.

If ``filename_or_fileobject`` is a string, then it is interpreted as a path to a WAV audio file on the filesystem. Otherwise, ``filename_or_fileobject`` should be a file-like object such as ``io.BytesIO`` or similar. In either case, the specified file is used as the audio source.

This class is to be used with ``with`` statements:

.. code:: python

    with WavFile("test.wav") as source:    # open the WAV file for reading
        pass                               # do things here - `source` is the WavFile instance created above

``wavfile_instance.DURATION``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Represents the length of the audio stored in the WAV file in seconds. This property is only available when inside a context - essentially, that means it should only be accessed inside a ``with wavfile_instance ...`` statement. Outside of contexts, this property is ``None``.

This is useful when combined with the ``offset`` parameter of ``recognizer_instance.record``, since when together it is possible to perform speech recognition in chunks.

However, note that recognizing speech in multiple chunks is not the same as recognizing the whole thing at once. If spoken words appear on the boundaries that we split the audio into chunks on, each chunk only gets part of the word, which may result in inaccurate results.

``Recognizer(language = "en-US", key = "AIzaSyBOti4mM-6x9WDnZIjIeyEU21OpBXqWBgw")``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Creates a new ``Recognizer`` instance, which represents a collection of speech recognition functionality.

The language is determined by ``language``, a standard language code like `"en-US"` or `"en-GB"`, and defaults to US English. A list of supported language codes can be found `here <http://stackoverflow.com/questions/14257598/>`__. Basically, language codes can be just the language (``en``), or a language with a dialect (``en-US``).

The Google Speech Recognition API key is specified by ``key``. If not specified, it uses a generic key that works out of the box.

**WARNING: THE GENERIC KEY IS INTENDED FOR TESTING AND PERSONAL PURPOSES ONLY AND MAY BE REVOKED BY GOOGLE AT ANY TIME.**

If you need to use this module for purposes other than these, please obtain your own API key from Google. See the "Requirements" section for more information.

``recognizer_instance.energy_threshold = 300``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Represents the energy level threshold for sounds. Values below this threshold are considered silence, and values above this threshold are considered speech. Can be changed.

This is tweaked automatically if dynamic thresholds are enabled (see ``recognizer_instance.dynamic_energy_threshold``). A good starting value will generally allow automatic adjustment reach a good value faster.

This threshold is associated with the perceived loudness of the sound, but it is a nonlinear relationship. The actual energy threshold you will need depends on your microphone sensitivity or audio data. Typical values for a silent room are 0 to 100, and typical values for speaking are between 150 and 3500. Ambient noise has a significant impact on what values will work best.

If you're having trouble with the recognizer trying to recognize words even when you're not speaking, try tweaking this to a higher value. If you're having trouble with the recognizer not recognizing your words when you are speaking, try tweaking this to a lower value. For example, a sensitive microphone or microphones in louder rooms might have a ambient (non-speaking) energy level of up to 4000:

.. code:: python

    import speech_recognition as sr
    r = sr.Recognizer()
    r.energy_threshold = 4000
    # rest of your code goes here

The dynamic energy threshold setting can mitigate this by increasing or decreasing this automatically to account for ambient noise. However, this takes time to adjust, so it is still possible to get the false positive detections before the threshold settles into a good value. To avoid this, set this property to a high value initially (4000 works well), so the threshold is always above ambient noise levels.

``recognizer_instance.dynamic_energy_threshold = True``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Represents whether the energy level threshold (see ``recognizer_instance.energy_threshold``) for sounds should be automatically adjusted based on the currently ambient noise level while listening. Can be changed.

Recommended for situations where the ambient noise level is unpredictable, which seems to be the majority of use cases. If the ambient noise level is strictly controlled, better results might be achieved by setting this to ``False`` to turn it off.

``recognizer_instance.dynamic_energy_adjustment_damping = 0.15``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If the dynamic energy threshold setting is enabled (see ``recognizer_instance.dynamic_energy_threshold``), represents approximately the fraction of the current energy threshold that is retained after one second of dynamic threshold adjustment. Can be changed (not recommended).

Lower values allow for faster adjustment, but also make it more likely to miss certain phrases. This value should be between 0 and 1. As this value approaches 1, dynamic adjustment has less of an effect over time. When this value is 1, dynamic adjustment does nothing.

``recognizer_instance.dynamic_energy_adjustment_ratio = 1.5``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If the dynamic energy threshold setting is enabled (see ``recognizer_instance.dynamic_energy_threshold``), represents the minimum factor by which speech is louder than ambient noise. Can be changed (not recommended).

For example, the default value of 1.5 means that speech is at least 1.5 times louder than ambient noise. Smaller values result in more false positives but fewer false negatives when ambient noise is loud compared to speech.

``recognizer_instance.pause_threshold = 0.8``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Represents the minimum length of silence (in seconds) that will register as the end of a phrase. Can be changed.

Smaller values result in the recognition completing more quickly, but might result in slower speakers being cut off.

``recognizer_instance.record(source, duration = None, offset = None)``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Records up to ``duration`` seconds of audio from ``source`` (an ``AudioSource`` instance) starting at ``offset`` (or at the beginning if not specified) into an ``AudioData`` instance, which it returns.

If ``duration`` is not specified, then it will record until there is no more audio input.

``recognizer_instance.adjust_for_ambient_noise(source, duration = 1)``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Adjusts the energy threshold dynamically using audio from ``source`` (an ``AudioSource`` instance) to account for ambient noise.

Intended to calibrate the energy threshold with the ambient energy level. Should be used on periods of audio without speech - will stop early if any speech is detected.

The ``duration`` parameter is the maximum number of seconds that it will dynamically adjust the threshold for before returning. This value should be at least 0.5 in order to get a representative sample of the ambient noise.

``recognizer_instance.listen(source, timeout = None)``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Records a single phrase from ``source`` (an ``AudioSource`` instance) into an ``AudioData`` instance, which it returns.

This is done by waiting until the audio has an energy above ``recognizer_instance.energy_threshold`` (the user has started speaking), and then recording until it encounters ``recognizer_instance.pause_threshold`` seconds of silence or there is no more audio input. The ending silence is not included.

The ``timeout`` parameter is the maximum number of seconds that it will wait for a phrase to start before giving up and throwing a ``TimeoutError`` exception. If ``None``, it will wait indefinitely.

``recognizer_instance.listen_in_background(source, callback)``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Spawns a thread to repeatedly record phrases from ``source`` (an ``AudioSource`` instance) into an ``AudioData`` instance and call ``callback`` with that ``AudioData`` instance as soon as each phrase are detected.

Returns a function object that, when called, stops the background listener thread. The background thread is a daemon and will not stop the program from exiting if there are no other non-daemon threads.

Phrase recognition uses the exact same mechanism as ``recognizer_instance.listen(source)``.

The ``callback`` parameter is a function that should accept two parameters - the ``recognizer_instance``, and an ``AudioData`` instance representing the captured audio. Note that this function will be called from a non-main thread.

``recognizer_instance.recognize(audio_data, show_all = False)``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Performs speech recognition, using the Google Speech Recognition API, on ``audio_data`` (an ``AudioData`` instance).

Returns the most likely transcription if ``show_all`` is ``False``, otherwise it returns a ``dict`` of all possible transcriptions and their confidence levels.

Note: confidence is set to 0 if it isn't given by Google

Also raises a ``LookupError`` exception if the speech is unintelligible, a ``KeyError`` if the key isn't valid or the quota for the key has been maxed out, and ``IndexError`` if there is no internet connection.

Note: ``KeyError`` and ``IndexError`` is a subclass of ``LookupError`` so a ``LookupError`` will catch all three types of errors. To catch subclasses you must place their handler clause before ``LookupError``:

.. code:: python

    import speech_recognition as sr
    r = sr.Recognizer()
    with sr.WavFile("test.wav") as source:              # use "test.wav" as the audio source
        audio = r.record(source)                        # extract audio data from the file

    try:
        print("You said " + r.recognize(audio))         # recognize speech using Google Speech Recognition
    except IndexError:                                  # the API key didn't work
        print("No internet connection")
    except KeyError:                                    # the API key didn't work
        print("Invalid API key or quota maxed out")
    except LookupError:                                 # speech is unintelligible
        print("Could not understand audio")

``AudioSource``
~~~~~~~~~~~~~~~

Base class representing audio sources. Do not instantiate.

Instances of subclasses of this class, such as ``Microphone`` and ``WavFile``, can be passed to things like ``recognizer_instance.record`` and ``recognizer_instance.listen``.

``AudioData``
~~~~~~~~~~~~~

Storage class for audio data.

Contains the fields ``rate`` and ``data``, which represent the framerate and raw audio samples of the audio data, respectively.

Authors
-------

::

    Uberi <azhang9@gmail.com> (Anthony Zhang)
    bobsayshilol
    arvindch <achembarpu@gmail.com> (Arvind Chembarpu)
    kevinismith <kevin_i_smith@yahoo.com> (Kevin Smith)
    haas85
    DelightRun <changxu.mail@gmail.com>

Please report bugs and suggestions at the `issue tracker <https://github.com/Uberi/speech_recognition/issues>`__!

License
-------

Copyright 2014-2015 `Anthony Zhang (Uberi) <https://uberi.github.io>`__.

The source code is available online at `GitHub <https://github.com/Uberi/speech_recognition>`__.

This program is made available under the 3-clause BSD license. See ``LICENSE.txt`` in the project's root directory for more information.
