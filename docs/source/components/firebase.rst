Firebase - firebase_options.dart
================================

.. code-block:: dart

    class DefaultFirebaseOptions {
        static FirebaseOptions get currentPlatform {
            if (kIsWeb) {
            return web;
            }
            switch (defaultTargetPlatform) {
            case TargetPlatform.android:
                return android;
            case TargetPlatform.iOS:
                return ios;
            case TargetPlatform.macOS:
                return macos;
            case TargetPlatform.windows:
                throw UnsupportedError(
                'DefaultFirebaseOptions have not been configured for windows - '
                'you can reconfigure this by running the FlutterFire CLI again.',
                );
            case TargetPlatform.linux:
                throw UnsupportedError(
                'DefaultFirebaseOptions have not been configured for linux - '
                'you can reconfigure this by running the FlutterFire CLI again.',
                );
            default:
                throw UnsupportedError(
                'DefaultFirebaseOptions are not supported for this platform.',
                );
            }
        }

This file is used to determine the default Firebase options for the current platform. The default options are determined by the current platform and the target platform.