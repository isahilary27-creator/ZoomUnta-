# ZoomUnta-
ZoomUnta Social Platform 
<!DOCTYPE html>
<html>
<head>
  <title>ZoomUnta</title>
  <meta charset="UTF-8">
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; background: #f7f7f7; }
    input, textarea { width: 300px; margin: 5px 0; padding: 8px; }
    button { padding: 8px 12px; margin: 5px 0; cursor: pointer; }
    .post { background: #fff; padding: 10px; margin: 10px 0; border-radius: 5px; }
    .post h4 { margin: 0; }
    .post p { margin: 5px 0 0 0; }
    .post button { margin-right: 5px; }
  </style>

  <!-- Firebase SDKs -->
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore.js"></script>
</head>
<body>

  <h2>ZoomUnta - Register User</h2>
  <form id="registerForm">
    <input type="text" id="name" placeholder="Enter your name" required /><br>
    <input type="text" id="nationality" placeholder="Enter your nationality" required /><br>
    <button type="submit">Register</button>
  </form>

  <h2>Create a Post</h2>
  <form id="postForm">
    <input type="text" id="author" placeholder="Your name" required /><br>
    <textarea id="postText" placeholder="Write something..." required></textarea><br>
    <button type="submit">Post</button>
  </form>

  <h2>Posts</h2>
  <div id="postsContainer"></div>

  <script>
    // ===== FIREBASE CONFIG =====
    const firebaseConfig = {
      apiKey: "YOUR_API_KEY",
      authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
      projectId: "YOUR_PROJECT_ID",
      storageBucket: "YOUR_PROJECT_ID.appspot.com",
      messagingSenderId: "YOUR_SENDER_ID",
      appId: "YOUR_APP_ID"
    };

    // Initialize Firebase
    const app = firebase.initializeApp(firebaseConfig);
    const db = firebase.firestore();

    // ===== REGISTER USER =====
    const registerForm = document.getElementById('registerForm');
    registerForm.addEventListener('submit', async (e) => {
      e.preventDefault();
      const name = document.getElementById('name').value;
      const nationality = document.getElementById('nationality').value;

      try {
        await db.collection('users').add({
          name: name,
          nationality: nationality,
          timestamp: firebase.firestore.FieldValue.serverTimestamp()
        });
        alert('User registered!');
        registerForm.reset();
      } catch (err) {
        console.error(err);
        alert('Error registering user');
      }
    });

    // ===== CREATE A POST =====
    const postForm = document.getElementById('postForm');
    postForm.addEventListener('submit', async (e) => {
      e.preventDefault();
      const author = document.getElementById('author').value;
      const text = document.getElementById('postText').value;

      try {
        await db.collection('posts').add({
          author: author,
          text: text,
          timestamp: firebase.firestore.FieldValue.serverTimestamp()
        });
        postForm.reset();
      } catch (err) {
        console.error(err);
        alert('Error posting');
      }
    });

    // ===== DISPLAY POSTS IN REAL-TIME =====
    const postsContainer = document.getElementById('postsContainer');
    db.collection('posts').orderBy('timestamp', 'desc')
      .onSnapshot(snapshot => {
        postsContainer.innerHTML = '';
        snapshot.forEach(doc => {
          const post = doc.data();
          const div = document.createElement('div');
          div.className = 'post';
          div.innerHTML = `
            <h4>${post.author}</h4>
            <p id="text-${doc.id}">${post.text}</p>
            <button onclick="editPost('${doc.id}', '${post.text}')">Edit</button>
            <button onclick="deletePost('${doc.id}')">Delete</button>
          `;
          postsContainer.appendChild(div);
        });
      });

    // ===== EDIT POST =====
    function editPost(postId, oldText) {
      const newText = prompt('Edit your post:', oldText);
      if (newText !== null && newText.trim() !== '') {
        db.collection('posts').doc(postId).update({
          text: newText
        })
        .then(() => alert('Post updated!'))
        .catch(err => {
          console.error(err);
          alert('Error updating post');
        });
      }
    }

    // ===== DELETE POST =====
    async function deletePost(postId) {
      if (confirm('Are you sure you want to delete this post?')) {
        try {
          await db.collection('posts').doc(postId).delete();
          alert('Post deleted!');
        } catch (err) {
          console.error(err);
          alert('Error deleting post');
        }
      }
    }
  </script>

</body>
</html>
