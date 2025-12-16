# drag-drop-uploader
<!DOCTYPE html>
<html>
<head>
  <title>Drag And Drop Images</title>

  <style>
    #file-input {
      display: none;
    }

    #drop-area {
      width: 300px;
      height: 200px;
      border: 2px dashed #888;
      display: flex;
      justify-content: center;
      align-items: center;
      cursor: pointer;
      border-radius: 8px;
      background-color: #f9f9f9;
      text-align: center;
      flex-direction: column;
    }

    #drop-area:hover {
      background-color: #eee;
    }

    #drop-area img {
      max-width: 100%;
      max-height: 100%;
    }

    #progress-container {
      width: 90%;
      height: 10px;
      background: #ddd;
      border-radius: 5px;
      margin-top: 10px;
      display: none;
    }

    #progress-bar {
      height: 100%;
      width: 0%;
      background: #4caf50;
      border-radius: 5px;
      transition: width 0.3s ease;
    }

    #gallery {
      margin-top: 20px;
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
    }

    #gallery img {
      width: 100px;
      height: 100px;
      object-fit: cover;
      border-radius: 6px;
      border: 1px solid #ccc;
    }
  </style>
</head>

<body>

  <div id="drop-area">
    <input id="file-input" type="file" accept="image/*">
    <p id="text">Drag and Drop image here<br>or click to select</p>

    <div id="progress-container">
      <div id="progress-bar"></div>
    </div>
  </div>

  <div id="gallery"></div>

  <script>
    const dropArea = document.getElementById("drop-area");
    const fileInput = document.getElementById("file-input");
    const progressContainer = document.getElementById("progress-container");
    const progressBar = document.getElementById("progress-bar");
    const text = document.getElementById("text");
    const gallery = document.getElementById("gallery");

    // Load images from localStorage on page load
    window.onload = function () {
      const savedImages = JSON.parse(localStorage.getItem("uploadedImages")) || [];
      savedImages.forEach(imgData => addImageToGallery(imgData));
    };

    dropArea.addEventListener("click", () => fileInput.click());

    dropArea.addEventListener("dragover", e => {
      e.preventDefault();
      dropArea.style.backgroundColor = "#ddd";
    });

    dropArea.addEventListener("dragleave", () => {
      dropArea.style.backgroundColor = "#f9f9f9";
    });

    dropArea.addEventListener("drop", e => {
      e.preventDefault();
      dropArea.style.backgroundColor = "#f9f9f9";
      handleFile(e.dataTransfer.files[0]);
    });

    fileInput.addEventListener("change", () => {
      handleFile(fileInput.files[0]);
    });

    function handleFile(file) {
      if (!file || !file.type.startsWith("image/")) {
        alert("Only image files allowed");
        return;
      }

      progressBar.style.width = "0%";
      progressContainer.style.display = "block";
      text.style.display = "none";

      let progress = 0;
      const fakeUpload = setInterval(() => {
        progress += 10;
        progressBar.style.width = progress + "%";

        if (progress >= 100) {
          clearInterval(fakeUpload);
          progressContainer.style.display = "none";
          saveAndShowImage(file);
        }
      }, 200);
    }

    function saveAndShowImage(file) {
      const reader = new FileReader();

      reader.onload = () => {
        const imageData = reader.result;

        // Get existing images from localStorage
        let images = JSON.parse(localStorage.getItem("uploadedImages")) || [];
        images.push(imageData);
        localStorage.setItem("uploadedImages", JSON.stringify(images));

        // Add image to gallery
        addImageToGallery(imageData);
      };

      reader.readAsDataURL(file);
    }

    function addImageToGallery(imageData) {
      const img = document.createElement("img");
      img.src = imageData;
      gallery.appendChild(img);
    }
  </script>

</body>
</html>
