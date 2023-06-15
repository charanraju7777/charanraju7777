<!DOCTYPE html>
<html>
<head>
    <title>Image Background Remover</title>
    <style>
        canvas {
            border: 1px solid black;
        }

        .download-btn {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            text-decoration: none;
            margin-top: 10px;
            display: inline-block;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <input type="file" id="image-input" accept="image/*" />
    <canvas id="canvas"></canvas>
    <button id="download-btn" class="download-btn" disabled>Download</button>

    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        let imageData;

        // Function to handle the image selection
        function handleImageUpload(event) {
            const file = event.target.files[0];
            const reader = new FileReader();

            reader.onload = function (e) {
                const img = new Image();
                img.onload = function () {
                    canvas.width = img.width;
                    canvas.height = img.height;
                    ctx.drawImage(img, 0, 0);
                    imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
                    document.getElementById('download-btn').disabled = false;
                };
                img.src = e.target.result;
            };

            reader.readAsDataURL(file);
        }

        // Function to remove the background
        function removeBackground() {
            // Add your background removal logic here
            // Modify the imageData object or perform any necessary image processing

            // Example: Set the background pixels to transparent
            const data = imageData.data;
            for (let i = 0; i < data.length; i += 4) {
                const red = data[i];
                const green = data[i + 1];
                const blue = data[i + 2];

                // Check if the pixel matches the background color
                if (red === 255 && green === 255 && blue === 255) {
                    data[i + 3] = 0; // Set alpha channel to 0 (transparent)
                }
            }

            // Update the canvas with the modified image data
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.putImageData(imageData, 0, 0);
        }

        // Function to handle the download button click
        function handleDownload() {
            removeBackground();
            const downloadLink = document.createElement('a');
            downloadLink.href = canvas.toDataURL('image/png');
            downloadLink.download = 'background_removed.png';
            downloadLink.click();
        }

        // Attach event listeners
        const imageInput = document.getElementById('image-input');
        imageInput.addEventListener('change', handleImageUpload);

        const downloadBtn = document.getElementById('download-btn');
        downloadBtn.addEventListener('click', handleDownload);
    </script>
</body>
</html>
