# 54-hertz
Demo
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Media Platform</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: white;
            overflow: hidden;
        }

        #canvas {
            position: relative;
            width: 100vw;
            height: 100vh;
            overflow: hidden;
        }

        .media-item {
            position: absolute;
            cursor: move;
        }

        .media-item img, .media-item video, .media-item audio {
            max-width: 300px;
            max-height: 300px;
        }

        #toolbar {
            position: fixed;
            top: 10px;
            left: 10px;
            background-color: rgba(255, 255, 255, 0.8);
            padding: 10px;
            border-radius: 5px;
            z-index: 1000;
        }

        #toolbar input, #toolbar button {
            margin: 5px;
        }

        .resizable {
            resize: both;
            overflow: hidden;
        }

        .text-box {
            border: 1px solid #000;
            padding: 5px;
            min-width: 100px;
            min-height: 50px;
            font-family: Arial, sans-serif;
            white-space: pre-wrap;
        }
    </style>
</head>
<body>
    <div id="toolbar">
        <input type="file" id="fileInput" accept="image/*,video/*,audio/*">
        <button id="addText">Add Text</button>
        <input type="color" id="textColor" value="#000000">
    </div>

    <div id="canvas"></div>

    <script>
        const canvas = document.getElementById('canvas');
        const fileInput = document.getElementById('fileInput');
        const addTextButton = document.getElementById('addText');
        const textColorInput = document.getElementById('textColor');

        function makeDraggableAndResizable(element) {
            element.draggable = true;
            element.addEventListener('dragstart', (e) => {
                e.dataTransfer.setData('text/plain', 'dragging');
                element.dataset.offsetX = e.offsetX;
                element.dataset.offsetY = e.offsetY;
            });

            element.addEventListener('dragend', (e) => {
                element.style.left = `${e.pageX - element.dataset.offsetX}px`;
                element.style.top = `${e.pageY - element.dataset.offsetY}px`;
            });

            if (!element.classList.contains('text-box')) {
                element.classList.add('resizable');
            }
        }

        fileInput.addEventListener('change', (e) => {
            const file = e.target.files[0];
            if (!file) return;

            const mediaItem = document.createElement('div');
            mediaItem.classList.add('media-item');

            const url = URL.createObjectURL(file);
            let mediaElement;
            if (file.type.startsWith('image/')) {
                mediaElement = document.createElement('img');
                mediaElement.src = url;
            } else if (file.type.startsWith('video/')) {
                mediaElement = document.createElement('video');
                mediaElement.src = url;
                mediaElement.controls = true;
            } else if (file.type.startsWith('audio/')) {
                mediaElement = document.createElement('audio');
                mediaElement.src = url;
                mediaElement.controls = true;
            }

            if (mediaElement) {
                mediaItem.appendChild(mediaElement);
                canvas.appendChild(mediaItem);
                makeDraggableAndResizable(mediaItem);
            }

            fileInput.value = '';
        });

        addTextButton.addEventListener('click', () => {
            const textBox = document.createElement('div');
            textBox.contentEditable = true;
            textBox.classList.add('media-item', 'text-box');
            textBox.style.color = textColorInput.value;
            textBox.textContent = 'Double-click to edit';
            canvas.appendChild(textBox);
            makeDraggableAndResizable(textBox);
        });

        textColorInput.addEventListener('input', (e) => {
            const activeElement = document.activeElement;
            if (activeElement.classList.contains('text-box')) {
                activeElement.style.color = e.target.value;
            }
        });
    </script>
</body>
</html>
