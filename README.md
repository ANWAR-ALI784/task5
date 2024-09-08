# task5



import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:firebase_auth/firebase_auth.dart';
import 'package:cloud_firestore/cloud_firestore.dart';


void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  final GoogleSignIn _googleSignIn = GoogleSignIn();

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('Firebase Integration'),
        ),
        body: Center(
          child: ElevatedButton(
            child: Text('Sign in with Google'),
            onPressed: () async {
              final GoogleSignInAccount googleUser = await _googleSignIn.signIn();
              final GoogleSignInAuthentication googleAuth = await googleUser.authentication;
              final AuthCredential credential = GoogleAuthProvider.credential(
                accessToken: googleAuth.accessToken,
                idToken: googleAuth.idToken,
              );
              final User user = (await FirebaseAuth.instance.signInWithCredential(credential)).user;
              print(user.uid);
              await FirebaseFirestore.instance.collection('users').doc(user.uid).set({
                'name': user.displayName,
                'email': user.email,
              });
            },
          ),
        ),
      ),
    );
  }
}


