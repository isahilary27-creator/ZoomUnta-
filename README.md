# ZoomUnta-

<!DOCTYPE html>
<html>
<head>
  <title>ZoomUnta</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 15px;
    }
    h1 {
      text-align: center;
    }
    input, textarea, button {
      width: 100%;
      padding: 10px;
      margin-top: 10px;
      box-sizing: border-box;
    }
    button {
      background: #000;
      color: #fff;
      border: none;
      cursor: pointer;
    }
    .post {
      border: 1px solid #ccc;
      padding: 10px;
      margin-top: 15px;
    }
  </style>
</head>

<body>

<h1>
  <span style="color: blue;">Zoom</span><span style="color: orange;">Unta</span>
</h1>

<input id="username" placeholder="Your name">
<textarea id="postText" placeholder="Write something..."></textarea>
<button onclick="addPost()">Post</button>

<div id="feed"></div>

<!-- Firebase App (the core Firebase SDK) -->
<script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-firestore.js"></script>

<script>
  // Your Firebase configuration
  const firebaseConfig = {
    apiKey: "AIzaSyBQXx4AgjmjFhWTQEGZFJ8gyjhrGG36T04",
    authDomain: "zoomunta.firebaseapp.com",
    projectId: "zoomunta",
    storageBucket: "zoomunta.appspot.com",
    messagingSenderId: "490604667032",
    appId: "1:490604667032:web:088173f78346086d14f0ed"
  };

  // Initialize Firebase
  const app = firebase.initializeApp(firebaseConfig);
  const db = firebase.firestore();

  // Load existing posts from Firestore
  const feedDiv = document.getElementById("feed");

  db.collection("posts").orderBy("timestamp", "desc").get().then(snapshot => {
    snapshot.forEach(doc => {
      const post = doc.data();
      const div = document.createElement("div");
      div.className = "post";
      div.innerHTML = `<strong>${post.name}</strong><p>${post.text}</p>`;
      feedDiv.appendChild(div);
    });
  });

  function addPost() {
    const name = document.getElementById("username").value.trim();
    const text = document.getElementById("postText").value.trim();

    if (name === "" || text === "") {
      alert("Please enter name and post");
      return;
    }

    const postData = { 
      name: name, 
      text: text, 
      timestamp: firebase.firestore.FieldValue.serverTimestamp() 
    };

    db.collection("posts").add(postData).then(() => {
      const div = document.createElement("div");
      div.className = "post";
      div.innerHTML = `<strong>${name}</strong><p>${text}</p>`;
      feedDiv.prepend(div);
      document.getElementById("postText").value = "";
    }).catch(err => {
      console.error("Error adding post: ", err);
    });
  }
</script>

</body>
</html>
