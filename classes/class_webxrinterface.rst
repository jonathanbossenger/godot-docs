:github_url: hide

.. DO NOT EDIT THIS FILE!!!
.. Generated automatically from Godot engine sources.
.. Generator: https://github.com/godotengine/godot/tree/master/doc/tools/make_rst.py.
.. XML source: https://github.com/godotengine/godot/tree/master/modules/webxr/doc_classes/WebXRInterface.xml.

.. _class_WebXRInterface:

WebXRInterface
==============

**Inherits:** :ref:`XRInterface<class_XRInterface>` **<** :ref:`RefCounted<class_RefCounted>` **<** :ref:`Object<class_Object>`

XR interface using WebXR.

.. rst-class:: classref-introduction-group

Description
-----------

WebXR is an open standard that allows creating VR and AR applications that run in the web browser.

As such, this interface is only available when running in Web exports.

WebXR supports a wide range of devices, from the very capable (like Valve Index, HTC Vive, Oculus Rift and Quest) down to the much less capable (like Google Cardboard, Oculus Go, GearVR, or plain smartphones).

Since WebXR is based on JavaScript, it makes extensive use of callbacks, which means that **WebXRInterface** is forced to use signals, where other XR interfaces would instead use functions that return a result immediately. This makes **WebXRInterface** quite a bit more complicated to initialize than other XR interfaces.

Here's the minimum code required to start an immersive VR session:

::

    extends Node3D

    var webxr_interface
    var vr_supported = false

    func _ready():
        # We assume this node has a button as a child.
        # This button is for the user to consent to entering immersive VR mode.
        $Button.pressed.connect(self._on_button_pressed)

        webxr_interface = XRServer.find_interface("WebXR")
        if webxr_interface:
            # WebXR uses a lot of asynchronous callbacks, so we connect to various
            # signals in order to receive them.
            webxr_interface.session_supported.connect(self._webxr_session_supported)
            webxr_interface.session_started.connect(self._webxr_session_started)
            webxr_interface.session_ended.connect(self._webxr_session_ended)
            webxr_interface.session_failed.connect(self._webxr_session_failed)

            # This returns immediately - our _webxr_session_supported() method
            # (which we connected to the "session_supported" signal above) will
            # be called sometime later to let us know if it's supported or not.
            webxr_interface.is_session_supported("immersive-vr")

    func _webxr_session_supported(session_mode, supported):
        if session_mode == 'immersive-vr':
            vr_supported = supported

    func _on_button_pressed():
        if not vr_supported:
            OS.alert("Your browser doesn't support VR")
            return

        # We want an immersive VR session, as opposed to AR ('immersive-ar') or a
        # simple 3DoF viewer ('viewer').
        webxr_interface.session_mode = 'immersive-vr'
        # 'bounded-floor' is room scale, 'local-floor' is a standing or sitting
        # experience (it puts you 1.6m above the ground if you have 3DoF headset),
        # whereas as 'local' puts you down at the XROrigin.
        # This list means it'll first try to request 'bounded-floor', then
        # fallback on 'local-floor' and ultimately 'local', if nothing else is
        # supported.
        webxr_interface.requested_reference_space_types = 'bounded-floor, local-floor, local'
        # In order to use 'local-floor' or 'bounded-floor' we must also
        # mark the features as required or optional. By including 'hand-tracking'
        # as an optional feature, it will be enabled if supported.
        webxr_interface.required_features = 'local-floor'
        webxr_interface.optional_features = 'bounded-floor, hand-tracking'

        # This will return false if we're unable to even request the session,
        # however, it can still fail asynchronously later in the process, so we
        # only know if it's really succeeded or failed when our
        # _webxr_session_started() or _webxr_session_failed() methods are called.
        if not webxr_interface.initialize():
            OS.alert("Failed to initialize")
            return

    func _webxr_session_started():
        $Button.visible = false
        # This tells Godot to start rendering to the headset.
        get_viewport().use_xr = true
        # This will be the reference space type you ultimately got, out of the
        # types that you requested above. This is useful if you want the game to
        # work a little differently in 'bounded-floor' versus 'local-floor'.
        print("Reference space type: ", webxr_interface.reference_space_type)
        # This will be the list of features that were successfully enabled
        # (except on browsers that don't support this property).
        print("Enabled features: ", webxr_interface.enabled_features)

    func _webxr_session_ended():
        $Button.visible = true
        # If the user exits immersive mode, then we tell Godot to render to the web
        # page again.
        get_viewport().use_xr = false

    func _webxr_session_failed(message):
        OS.alert("Failed to initialize: " + message)

There are a couple ways to handle "controller" input:

- Using :ref:`XRController3D<class_XRController3D>` nodes and their :ref:`XRController3D.button_pressed<class_XRController3D_signal_button_pressed>` and :ref:`XRController3D.button_released<class_XRController3D_signal_button_released>` signals. This is how controllers are typically handled in XR apps in Godot, however, this will only work with advanced VR controllers like the Oculus Touch or Index controllers, for example.

- Using the :ref:`select<class_WebXRInterface_signal_select>`, :ref:`squeeze<class_WebXRInterface_signal_squeeze>` and related signals. This method will work for both advanced VR controllers, and non-traditional input sources like a tap on the screen, a spoken voice command or a button press on the device itself.

You can use both methods to allow your game or app to support a wider or narrower set of devices and input methods, or to allow more advanced interactions with more advanced devices.

.. rst-class:: classref-introduction-group

Tutorials
---------

- `How to make a VR game for WebXR with Godot 4 <https://www.snopekgames.com/tutorial/2023/how-make-vr-game-webxr-godot-4>`__

.. rst-class:: classref-reftable-group

Properties
----------

.. table::
   :widths: auto

   +-----------------------------+-------------------------------------------------------------------------------------------------------+
   | :ref:`String<class_String>` | :ref:`enabled_features<class_WebXRInterface_property_enabled_features>`                               |
   +-----------------------------+-------------------------------------------------------------------------------------------------------+
   | :ref:`String<class_String>` | :ref:`optional_features<class_WebXRInterface_property_optional_features>`                             |
   +-----------------------------+-------------------------------------------------------------------------------------------------------+
   | :ref:`String<class_String>` | :ref:`reference_space_type<class_WebXRInterface_property_reference_space_type>`                       |
   +-----------------------------+-------------------------------------------------------------------------------------------------------+
   | :ref:`String<class_String>` | :ref:`requested_reference_space_types<class_WebXRInterface_property_requested_reference_space_types>` |
   +-----------------------------+-------------------------------------------------------------------------------------------------------+
   | :ref:`String<class_String>` | :ref:`required_features<class_WebXRInterface_property_required_features>`                             |
   +-----------------------------+-------------------------------------------------------------------------------------------------------+
   | :ref:`String<class_String>` | :ref:`session_mode<class_WebXRInterface_property_session_mode>`                                       |
   +-----------------------------+-------------------------------------------------------------------------------------------------------+
   | :ref:`String<class_String>` | :ref:`visibility_state<class_WebXRInterface_property_visibility_state>`                               |
   +-----------------------------+-------------------------------------------------------------------------------------------------------+

.. rst-class:: classref-reftable-group

Methods
-------

.. table::
   :widths: auto

   +---------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Array<class_Array>`                               | :ref:`get_available_display_refresh_rates<class_WebXRInterface_method_get_available_display_refresh_rates>`\ (\ ) |const|                                    |
   +---------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`float<class_float>`                               | :ref:`get_display_refresh_rate<class_WebXRInterface_method_get_display_refresh_rate>`\ (\ ) |const|                                                          |
   +---------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`TargetRayMode<enum_WebXRInterface_TargetRayMode>` | :ref:`get_input_source_target_ray_mode<class_WebXRInterface_method_get_input_source_target_ray_mode>`\ (\ input_source_id\: :ref:`int<class_int>`\ ) |const| |
   +---------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`XRControllerTracker<class_XRControllerTracker>`   | :ref:`get_input_source_tracker<class_WebXRInterface_method_get_input_source_tracker>`\ (\ input_source_id\: :ref:`int<class_int>`\ ) |const|                 |
   +---------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`bool<class_bool>`                                 | :ref:`is_input_source_active<class_WebXRInterface_method_is_input_source_active>`\ (\ input_source_id\: :ref:`int<class_int>`\ ) |const|                     |
   +---------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | |void|                                                  | :ref:`is_session_supported<class_WebXRInterface_method_is_session_supported>`\ (\ session_mode\: :ref:`String<class_String>`\ )                              |
   +---------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | |void|                                                  | :ref:`set_display_refresh_rate<class_WebXRInterface_method_set_display_refresh_rate>`\ (\ refresh_rate\: :ref:`float<class_float>`\ )                        |
   +---------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. rst-class:: classref-section-separator

----

.. rst-class:: classref-descriptions-group

Signals
-------

.. _class_WebXRInterface_signal_display_refresh_rate_changed:

.. rst-class:: classref-signal

**display_refresh_rate_changed**\ (\ ) :ref:`🔗<class_WebXRInterface_signal_display_refresh_rate_changed>`

Emitted after the display's refresh rate has changed.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_reference_space_reset:

.. rst-class:: classref-signal

**reference_space_reset**\ (\ ) :ref:`🔗<class_WebXRInterface_signal_reference_space_reset>`

Emitted to indicate that the reference space has been reset or reconfigured.

When (or whether) this is emitted depends on the user's browser or device, but may include when the user has changed the dimensions of their play space (which you may be able to access via :ref:`XRInterface.get_play_area()<class_XRInterface_method_get_play_area>`) or pressed/held a button to recenter their position.

See `WebXR's XRReferenceSpace reset event <https://developer.mozilla.org/en-US/docs/Web/API/XRReferenceSpace/reset_event>`__ for more information.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_select:

.. rst-class:: classref-signal

**select**\ (\ input_source_id\: :ref:`int<class_int>`\ ) :ref:`🔗<class_WebXRInterface_signal_select>`

Emitted after one of the input sources has finished its "primary action".

Use :ref:`get_input_source_tracker()<class_WebXRInterface_method_get_input_source_tracker>` and :ref:`get_input_source_target_ray_mode()<class_WebXRInterface_method_get_input_source_target_ray_mode>` to get more information about the input source.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_selectend:

.. rst-class:: classref-signal

**selectend**\ (\ input_source_id\: :ref:`int<class_int>`\ ) :ref:`🔗<class_WebXRInterface_signal_selectend>`

Emitted when one of the input sources has finished its "primary action".

Use :ref:`get_input_source_tracker()<class_WebXRInterface_method_get_input_source_tracker>` and :ref:`get_input_source_target_ray_mode()<class_WebXRInterface_method_get_input_source_target_ray_mode>` to get more information about the input source.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_selectstart:

.. rst-class:: classref-signal

**selectstart**\ (\ input_source_id\: :ref:`int<class_int>`\ ) :ref:`🔗<class_WebXRInterface_signal_selectstart>`

Emitted when one of the input source has started its "primary action".

Use :ref:`get_input_source_tracker()<class_WebXRInterface_method_get_input_source_tracker>` and :ref:`get_input_source_target_ray_mode()<class_WebXRInterface_method_get_input_source_target_ray_mode>` to get more information about the input source.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_session_ended:

.. rst-class:: classref-signal

**session_ended**\ (\ ) :ref:`🔗<class_WebXRInterface_signal_session_ended>`

Emitted when the user ends the WebXR session (which can be done using UI from the browser or device).

At this point, you should do ``get_viewport().use_xr = false`` to instruct Godot to resume rendering to the screen.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_session_failed:

.. rst-class:: classref-signal

**session_failed**\ (\ message\: :ref:`String<class_String>`\ ) :ref:`🔗<class_WebXRInterface_signal_session_failed>`

Emitted by :ref:`XRInterface.initialize()<class_XRInterface_method_initialize>` if the session fails to start.

\ ``message`` may optionally contain an error message from WebXR, or an empty string if no message is available.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_session_started:

.. rst-class:: classref-signal

**session_started**\ (\ ) :ref:`🔗<class_WebXRInterface_signal_session_started>`

Emitted by :ref:`XRInterface.initialize()<class_XRInterface_method_initialize>` if the session is successfully started.

At this point, it's safe to do ``get_viewport().use_xr = true`` to instruct Godot to start rendering to the XR device.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_session_supported:

.. rst-class:: classref-signal

**session_supported**\ (\ session_mode\: :ref:`String<class_String>`, supported\: :ref:`bool<class_bool>`\ ) :ref:`🔗<class_WebXRInterface_signal_session_supported>`

Emitted by :ref:`is_session_supported()<class_WebXRInterface_method_is_session_supported>` to indicate if the given ``session_mode`` is supported or not.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_squeeze:

.. rst-class:: classref-signal

**squeeze**\ (\ input_source_id\: :ref:`int<class_int>`\ ) :ref:`🔗<class_WebXRInterface_signal_squeeze>`

Emitted after one of the input sources has finished its "primary squeeze action".

Use :ref:`get_input_source_tracker()<class_WebXRInterface_method_get_input_source_tracker>` and :ref:`get_input_source_target_ray_mode()<class_WebXRInterface_method_get_input_source_target_ray_mode>` to get more information about the input source.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_squeezeend:

.. rst-class:: classref-signal

**squeezeend**\ (\ input_source_id\: :ref:`int<class_int>`\ ) :ref:`🔗<class_WebXRInterface_signal_squeezeend>`

Emitted when one of the input sources has finished its "primary squeeze action".

Use :ref:`get_input_source_tracker()<class_WebXRInterface_method_get_input_source_tracker>` and :ref:`get_input_source_target_ray_mode()<class_WebXRInterface_method_get_input_source_target_ray_mode>` to get more information about the input source.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_squeezestart:

.. rst-class:: classref-signal

**squeezestart**\ (\ input_source_id\: :ref:`int<class_int>`\ ) :ref:`🔗<class_WebXRInterface_signal_squeezestart>`

Emitted when one of the input sources has started its "primary squeeze action".

Use :ref:`get_input_source_tracker()<class_WebXRInterface_method_get_input_source_tracker>` and :ref:`get_input_source_target_ray_mode()<class_WebXRInterface_method_get_input_source_target_ray_mode>` to get more information about the input source.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_signal_visibility_state_changed:

.. rst-class:: classref-signal

**visibility_state_changed**\ (\ ) :ref:`🔗<class_WebXRInterface_signal_visibility_state_changed>`

Emitted when :ref:`visibility_state<class_WebXRInterface_property_visibility_state>` has changed.

.. rst-class:: classref-section-separator

----

.. rst-class:: classref-descriptions-group

Enumerations
------------

.. _enum_WebXRInterface_TargetRayMode:

.. rst-class:: classref-enumeration

enum **TargetRayMode**: :ref:`🔗<enum_WebXRInterface_TargetRayMode>`

.. _class_WebXRInterface_constant_TARGET_RAY_MODE_UNKNOWN:

.. rst-class:: classref-enumeration-constant

:ref:`TargetRayMode<enum_WebXRInterface_TargetRayMode>` **TARGET_RAY_MODE_UNKNOWN** = ``0``

We don't know the target ray mode.

.. _class_WebXRInterface_constant_TARGET_RAY_MODE_GAZE:

.. rst-class:: classref-enumeration-constant

:ref:`TargetRayMode<enum_WebXRInterface_TargetRayMode>` **TARGET_RAY_MODE_GAZE** = ``1``

Target ray originates at the viewer's eyes and points in the direction they are looking.

.. _class_WebXRInterface_constant_TARGET_RAY_MODE_TRACKED_POINTER:

.. rst-class:: classref-enumeration-constant

:ref:`TargetRayMode<enum_WebXRInterface_TargetRayMode>` **TARGET_RAY_MODE_TRACKED_POINTER** = ``2``

Target ray from a handheld pointer, most likely a VR touch controller.

.. _class_WebXRInterface_constant_TARGET_RAY_MODE_SCREEN:

.. rst-class:: classref-enumeration-constant

:ref:`TargetRayMode<enum_WebXRInterface_TargetRayMode>` **TARGET_RAY_MODE_SCREEN** = ``3``

Target ray from touch screen, mouse or other tactile input device.

.. rst-class:: classref-section-separator

----

.. rst-class:: classref-descriptions-group

Property Descriptions
---------------------

.. _class_WebXRInterface_property_enabled_features:

.. rst-class:: classref-property

:ref:`String<class_String>` **enabled_features** :ref:`🔗<class_WebXRInterface_property_enabled_features>`

.. rst-class:: classref-property-setget

- :ref:`String<class_String>` **get_enabled_features**\ (\ )

A comma-separated list of features that were successfully enabled by :ref:`XRInterface.initialize()<class_XRInterface_method_initialize>` when setting up the WebXR session.

This may include features requested by setting :ref:`required_features<class_WebXRInterface_property_required_features>` and :ref:`optional_features<class_WebXRInterface_property_optional_features>`, and will only be available after :ref:`session_started<class_WebXRInterface_signal_session_started>` has been emitted.

\ **Note:** This may not be support by all web browsers, in which case it will be an empty string.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_property_optional_features:

.. rst-class:: classref-property

:ref:`String<class_String>` **optional_features** :ref:`🔗<class_WebXRInterface_property_optional_features>`

.. rst-class:: classref-property-setget

- |void| **set_optional_features**\ (\ value\: :ref:`String<class_String>`\ )
- :ref:`String<class_String>` **get_optional_features**\ (\ )

A comma-seperated list of optional features used by :ref:`XRInterface.initialize()<class_XRInterface_method_initialize>` when setting up the WebXR session.

If a user's browser or device doesn't support one of the given features, initialization will continue, but you won't be able to use the requested feature.

This doesn't have any effect on the interface when already initialized.

Possible values come from `WebXR's XRReferenceSpaceType <https://developer.mozilla.org/en-US/docs/Web/API/XRReferenceSpaceType>`__, or include other features like ``"hand-tracking"`` to enable hand tracking.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_property_reference_space_type:

.. rst-class:: classref-property

:ref:`String<class_String>` **reference_space_type** :ref:`🔗<class_WebXRInterface_property_reference_space_type>`

.. rst-class:: classref-property-setget

- :ref:`String<class_String>` **get_reference_space_type**\ (\ )

The reference space type (from the list of requested types set in the :ref:`requested_reference_space_types<class_WebXRInterface_property_requested_reference_space_types>` property), that was ultimately used by :ref:`XRInterface.initialize()<class_XRInterface_method_initialize>` when setting up the WebXR session.

Possible values come from `WebXR's XRReferenceSpaceType <https://developer.mozilla.org/en-US/docs/Web/API/XRReferenceSpaceType>`__. If you want to use a particular reference space type, it must be listed in either :ref:`required_features<class_WebXRInterface_property_required_features>` or :ref:`optional_features<class_WebXRInterface_property_optional_features>`.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_property_requested_reference_space_types:

.. rst-class:: classref-property

:ref:`String<class_String>` **requested_reference_space_types** :ref:`🔗<class_WebXRInterface_property_requested_reference_space_types>`

.. rst-class:: classref-property-setget

- |void| **set_requested_reference_space_types**\ (\ value\: :ref:`String<class_String>`\ )
- :ref:`String<class_String>` **get_requested_reference_space_types**\ (\ )

A comma-seperated list of reference space types used by :ref:`XRInterface.initialize()<class_XRInterface_method_initialize>` when setting up the WebXR session.

The reference space types are requested in order, and the first one supported by the user's device or browser will be used. The :ref:`reference_space_type<class_WebXRInterface_property_reference_space_type>` property contains the reference space type that was ultimately selected.

This doesn't have any effect on the interface when already initialized.

Possible values come from `WebXR's XRReferenceSpaceType <https://developer.mozilla.org/en-US/docs/Web/API/XRReferenceSpaceType>`__. If you want to use a particular reference space type, it must be listed in either :ref:`required_features<class_WebXRInterface_property_required_features>` or :ref:`optional_features<class_WebXRInterface_property_optional_features>`.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_property_required_features:

.. rst-class:: classref-property

:ref:`String<class_String>` **required_features** :ref:`🔗<class_WebXRInterface_property_required_features>`

.. rst-class:: classref-property-setget

- |void| **set_required_features**\ (\ value\: :ref:`String<class_String>`\ )
- :ref:`String<class_String>` **get_required_features**\ (\ )

A comma-seperated list of required features used by :ref:`XRInterface.initialize()<class_XRInterface_method_initialize>` when setting up the WebXR session.

If a user's browser or device doesn't support one of the given features, initialization will fail and :ref:`session_failed<class_WebXRInterface_signal_session_failed>` will be emitted.

This doesn't have any effect on the interface when already initialized.

Possible values come from `WebXR's XRReferenceSpaceType <https://developer.mozilla.org/en-US/docs/Web/API/XRReferenceSpaceType>`__, or include other features like ``"hand-tracking"`` to enable hand tracking.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_property_session_mode:

.. rst-class:: classref-property

:ref:`String<class_String>` **session_mode** :ref:`🔗<class_WebXRInterface_property_session_mode>`

.. rst-class:: classref-property-setget

- |void| **set_session_mode**\ (\ value\: :ref:`String<class_String>`\ )
- :ref:`String<class_String>` **get_session_mode**\ (\ )

The session mode used by :ref:`XRInterface.initialize()<class_XRInterface_method_initialize>` when setting up the WebXR session.

This doesn't have any effect on the interface when already initialized.

Possible values come from `WebXR's XRSessionMode <https://developer.mozilla.org/en-US/docs/Web/API/XRSessionMode>`__, including: ``"immersive-vr"``, ``"immersive-ar"``, and ``"inline"``.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_property_visibility_state:

.. rst-class:: classref-property

:ref:`String<class_String>` **visibility_state** :ref:`🔗<class_WebXRInterface_property_visibility_state>`

.. rst-class:: classref-property-setget

- :ref:`String<class_String>` **get_visibility_state**\ (\ )

Indicates if the WebXR session's imagery is visible to the user.

Possible values come from `WebXR's XRVisibilityState <https://developer.mozilla.org/en-US/docs/Web/API/XRVisibilityState>`__, including ``"hidden"``, ``"visible"``, and ``"visible-blurred"``.

.. rst-class:: classref-section-separator

----

.. rst-class:: classref-descriptions-group

Method Descriptions
-------------------

.. _class_WebXRInterface_method_get_available_display_refresh_rates:

.. rst-class:: classref-method

:ref:`Array<class_Array>` **get_available_display_refresh_rates**\ (\ ) |const| :ref:`🔗<class_WebXRInterface_method_get_available_display_refresh_rates>`

Returns display refresh rates supported by the current HMD. Only returned if this feature is supported by the web browser and after the interface has been initialized.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_method_get_display_refresh_rate:

.. rst-class:: classref-method

:ref:`float<class_float>` **get_display_refresh_rate**\ (\ ) |const| :ref:`🔗<class_WebXRInterface_method_get_display_refresh_rate>`

Returns the display refresh rate for the current HMD. Not supported on all HMDs and browsers. It may not report an accurate value until after using :ref:`set_display_refresh_rate()<class_WebXRInterface_method_set_display_refresh_rate>`.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_method_get_input_source_target_ray_mode:

.. rst-class:: classref-method

:ref:`TargetRayMode<enum_WebXRInterface_TargetRayMode>` **get_input_source_target_ray_mode**\ (\ input_source_id\: :ref:`int<class_int>`\ ) |const| :ref:`🔗<class_WebXRInterface_method_get_input_source_target_ray_mode>`

Returns the target ray mode for the given ``input_source_id``.

This can help interpret the input coming from that input source. See `XRInputSource.targetRayMode <https://developer.mozilla.org/en-US/docs/Web/API/XRInputSource/targetRayMode>`__ for more information.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_method_get_input_source_tracker:

.. rst-class:: classref-method

:ref:`XRControllerTracker<class_XRControllerTracker>` **get_input_source_tracker**\ (\ input_source_id\: :ref:`int<class_int>`\ ) |const| :ref:`🔗<class_WebXRInterface_method_get_input_source_tracker>`

Gets an :ref:`XRControllerTracker<class_XRControllerTracker>` for the given ``input_source_id``.

In the context of WebXR, an input source can be an advanced VR controller like the Oculus Touch or Index controllers, or even a tap on the screen, a spoken voice command or a button press on the device itself. When a non-traditional input source is used, interpret the position and orientation of the :ref:`XRPositionalTracker<class_XRPositionalTracker>` as a ray pointing at the object the user wishes to interact with.

Use this method to get information about the input source that triggered one of these signals:

- :ref:`selectstart<class_WebXRInterface_signal_selectstart>`\ 

- :ref:`select<class_WebXRInterface_signal_select>`\ 

- :ref:`selectend<class_WebXRInterface_signal_selectend>`\ 

- :ref:`squeezestart<class_WebXRInterface_signal_squeezestart>`\ 

- :ref:`squeeze<class_WebXRInterface_signal_squeeze>`\ 

- :ref:`squeezestart<class_WebXRInterface_signal_squeezestart>`

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_method_is_input_source_active:

.. rst-class:: classref-method

:ref:`bool<class_bool>` **is_input_source_active**\ (\ input_source_id\: :ref:`int<class_int>`\ ) |const| :ref:`🔗<class_WebXRInterface_method_is_input_source_active>`

Returns ``true`` if there is an active input source with the given ``input_source_id``.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_method_is_session_supported:

.. rst-class:: classref-method

|void| **is_session_supported**\ (\ session_mode\: :ref:`String<class_String>`\ ) :ref:`🔗<class_WebXRInterface_method_is_session_supported>`

Checks if the given ``session_mode`` is supported by the user's browser.

Possible values come from `WebXR's XRSessionMode <https://developer.mozilla.org/en-US/docs/Web/API/XRSessionMode>`__, including: ``"immersive-vr"``, ``"immersive-ar"``, and ``"inline"``.

This method returns nothing, instead it emits the :ref:`session_supported<class_WebXRInterface_signal_session_supported>` signal with the result.

.. rst-class:: classref-item-separator

----

.. _class_WebXRInterface_method_set_display_refresh_rate:

.. rst-class:: classref-method

|void| **set_display_refresh_rate**\ (\ refresh_rate\: :ref:`float<class_float>`\ ) :ref:`🔗<class_WebXRInterface_method_set_display_refresh_rate>`

Sets the display refresh rate for the current HMD. Not supported on all HMDs and browsers. It won't take effect right away until after :ref:`display_refresh_rate_changed<class_WebXRInterface_signal_display_refresh_rate_changed>` is emitted.

.. |virtual| replace:: :abbr:`virtual (This method should typically be overridden by the user to have any effect.)`
.. |required| replace:: :abbr:`required (This method is required to be overridden when extending its base class.)`
.. |const| replace:: :abbr:`const (This method has no side effects. It doesn't modify any of the instance's member variables.)`
.. |vararg| replace:: :abbr:`vararg (This method accepts any number of arguments after the ones described here.)`
.. |constructor| replace:: :abbr:`constructor (This method is used to construct a type.)`
.. |static| replace:: :abbr:`static (This method doesn't need an instance to be called, so it can be called directly using the class name.)`
.. |operator| replace:: :abbr:`operator (This method describes a valid operator to use with this type as left-hand operand.)`
.. |bitfield| replace:: :abbr:`BitField (This value is an integer composed as a bitmask of the following flags.)`
.. |void| replace:: :abbr:`void (No return value.)`
