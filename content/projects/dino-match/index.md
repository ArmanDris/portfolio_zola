+++
authors = ["Arman Drismir"]
title = "Dino Match"
description = "Image similarity search app I built with an image vectorizer and a vector database."
date = 2024-07-27
[taxonomies]
tags = ["Python", "Vision Transformer", "Vector Database", "Svelte Kit"]
[extra]
+++
<p>
Dino match is a very cool project that mixes facebook's Dino vector embedder, Qdrant a vector database, and Svelte Kit a frontend web framework.
</p>
<p>
By combining all of these into a Flask app I am able to perform reverse image searches!
</p>
<p>
The app works by creating vector embeddings for all of the data in its <b>data</b> folder. The app then uploads the embeddings to QDrant. After QDrant has all the embeddings then the webstie is served. When a client opens the site it requests sample images to be sent by the backend. It then displays the sample images for the user. When the user clicks "Run Similarity Search" the image is Base64 encoded again and sent to the backend. Once the backend has the image it creates an embedding with it and queries QDrant using the embedding. Qdrant then responds with similar image embeddings and the backend sends these images back to the front end.
</p>
<p>
Since the backend can receive any Base64 encoded image a crafty user could easily send their own images to run similarity searches on. This also means that a small update could let users upload their own images but I doubt ill ever get to implementing that.
</p>
