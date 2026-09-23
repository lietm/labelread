Alcohol Label Scanner

  A lightweight, single-page web application that extracts structured data from alcohol beverage labels. Users can either upload an image or take a photo using their device's camera. The application uses AI to read the      label and parse the information into a structured format.

  The application uses a free tier of Gemini and has a limit of 10 to 15 Requests Per Minute.

  
Features

  Dual Input: Supports both file uploads and live camera capture.
  
  Client-Side Optimization: Resizes images locally in the browser using HTML5 Canvas to reduce API payload size and improve transmission speed.
  
  AI-Powered Extraction: Utilizes Google's Gemini Vision models to accurately identify and categorize label text. 
  
  Clean UI: Dark-themed, responsive interface designed for both desktop and mobile use.


Approach and Architecture

  The application is built as a monolithic, static HTML file containing all structure, styling, and logic.
  
  Capture/Upload: The user provides an image.
  
  Pre-processing: JavaScript captures the image data and draws it to a hidden <canvas> element. The image is proportionally downscaled to a maximum width/height of 800px.
  
  API Request: The resized image is converted to a base64 JPEG string and sent via a POST request to a backend proxy server.
  
  Data Extraction: The API prompt explicitly demands a JSON response containing specific keys (Brand name, Class/type, Alcohol content, Net contents, etc.).
  
  Display: The application parses the returned JSON, filters for the specific display fields required by the UI, and renders them on screen.


Error Handling

  The application includes built-in error handling to ensure a resilient user experience:
  
  Camera Permissions & Hardware: If the browser cannot access a camera (e.g., permissions are denied by the user, or no camera is attached to the device), the application catches the getUserMedia rejection and presents an   alert notifying the user that the camera is unavailable.
  
  API and Network Failures: If the network request to the backend proxy fails, the backend throws an error, or the AI model fails to return a parseable JSON response, the frontend catches the error gracefully. The UI        hides the loading state and displays a clear, red error message ("Error: The API request failed. Please retry in a minute.") rather than failing silently.


Tools Used

  Frontend: HTML5, CSS3, Vanilla JavaScript (No frameworks required).
  AI Model: Google Gemini API (gemini-3.5-flash-lite via generateContent endpoint).


Assumptions Made

  Environment: The application is hosted on a secure context (https:// or localhost). Browsers will block access to the camera device otherwise.
  
  Backend Proxy: The endpoint URL defined in index.html points to a serverless function or backend proxy (like Cloudflare Workers, AWS Lambda, or a Node.js Express server) that handles CORS and securely appends the Google   Gemini API key to the request. Do not expose your raw API key in the frontend code.
  
  Label Legibility: The uploaded or captured image is reasonably well-lit and the text is legible.


Integration & Extensibility Note

  The core extraction engine of this application returns data in a strict JSON format.
  
  While the frontend currently maps and displays five specific data points (Brand Name, Class/Type, Alcohol Content, Net Contents, and Government Warning), the underlying API request successfully extracts additional         metadata (like Bottler Address and Country of Origin). Because the output is standard JSON, it can be easily parsed and used as input to any other system, such as:


Setup and Deployment Instructions

  Because this is a static frontend application, it can be deployed to virtually any web server.


Local Development / Testing

  To test the camera functionality locally, you must run the file through a local web server (opening the file directly via file:/// will block camera access).
  
  Clone or download the repository.
  
  Start a local static server in the project directory.
  
    Python: python -m http.server 8000
  
    Node.js (serve): npx serve
  
    VS Code: Use the "Live Server" extension.
  
  Open your browser and navigate to http://localhost:8000 (or the port provided by your server).


General Production Deployment

  Setup a Backend Proxy: Set up a simple proxy server or serverless function that accepts a POST request, attaches your GEMINI_API_KEY securely as an environment variable, and forwards the payload to                         https://generativelanguage.googleapis.com/v1beta/models/gemini-3.5-flash-lite:generateContent.
  
  Update the Endpoint: Open index.html and update the const endpoint = "..." variable to point to your proxy's URL.
  
  Host the HTML: Upload index.html to any static hosting provider (e.g., AWS S3, GitHub Pages, Vercel, Netlify, or a standard Nginx/Apache server).
