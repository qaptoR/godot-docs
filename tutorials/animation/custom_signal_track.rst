.. _doc_custom_signal_track:

Custom Signal Track
===================

Introduction
------------

When working with an :ref:`AnimationPlayer <class_AnimationPlayer>` certain events may need to be triggered at specific points
during an animation, such as dealing damage during the 'hit' frame of an attack animation. 
However, while this feat could simply be accomplished with a single :ref:`call method track <>` for callaing a 'damage()' function
on an arbitrary script, a problem arises when there are ``n`` number of events that need to trigger at specific points in the animation.

This is where custom signal tracks come in, since an arbitrary number of methods can easily connect to one signal.
This also leverages the power of signals to connect methods as one-shot or even multiple times.

Adding the Custom Signal
------------------------

To do this we can add a custom signal somewhere using a script. For example, addding it to our ``AnimationPlayer``
itself helps encapsulate this approach, creating a single point of control for our animation events.
We can also set up multiple signals for different types of events, such as 'damage' and 'heal' if they require different
variety of parameters.

Additionally, there needs to be at least one function that will emit each added signal, however each signal can have multiple
emitters if there are significant differences to the logic of each emitter.

.. tabs::
 .. code-tab:: gdscript GDScript

    animation_key_signal(animation_name, key_name)

    func emit_animation_key_signal(animation_name : String, key_name : String) -> void:
        animation_key_signal.emit(animation_name, key_name)

 .. code-tab:: csharp

    [Signal]
    public delegate void AnimationKeySignalEventHandler(string animationName, string keyName);

    public void EmitAnimationKeySignal(string animationName, string keyName)
    {
        EmitSignal(SignalName.AnimationKeySignal, animationName, keyName);
    }

An additional benefit of this inbetween function call is that we can add additional logic to the function that may allow us to
pass flags or other switching data to the signal.

Adding the Call Method Track
----------------------------

Once the custome signal and emitter is defined the ``call method`` track can be added to the ``AnimationPlayer``.

.. image:: etc/etc.webp

Choose the desired emitter from the script and fill out the argument fields with the required values.

Remember to duplicate the keys to reuse whatever boilerplate arguments are required for the signal, such as the ``animation name``.
Other arguments such as ``key name`` should be unique for each key, allowing events to trigger precisely when needed.

``animation name`` is important for reuse of the signal template by multiple animations.
This allows methods that connect to the signal to know which animation the event is coming from.

.. note::
    Multiple call method tracks can be added to for the same node, so multiple methods can be called at the same time by
    adding additional tracks.

Connecting the Signal
---------------------

Now that the custom signal track has a key defined with a unique name, any script can connect to the signal using 
the right ``animation name`` and ``key name``.

.. tabs::
 .. code-tab:: gdscript GDScript

    func _ready() -> void:
        animation_player.connect("animation_key_signal", self, "_on_animation_key_signal")

    func _on_animation_key_signal(animation_name : String, key_name : String) -> void:
        if animation_name == "attack" and key_name == "damage":
            damage()

 .. code-tab:: csharp

    [Signal]
    public delegate void AnimationKeySignalEventHandler(string animationName, string keyName);

    public void EmitAnimationKeySignal(string animationName, string keyName)
    {
        EmitSignal(SignalName.AnimationKeySignal, animationName, keyName);
    }

