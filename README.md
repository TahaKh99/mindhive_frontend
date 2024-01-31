# MindHive Frontend Documentation

## Overview
This project implements a frontend web application using Vue.js. It interacts with a backend API to visualize coffee shop outlets on a map. The app allows users to view the locations of coffee shop outlets, their names, addresses, and displays a 5KM radius around each outlet.

## Technologies
- Vue.js 2
- Vue2-Leaflet for map visualization
- Axios for API requests
- Netlify for deployment

## Part 4: Frontend Development and Visualization
### Process
- Developed using Vue.js, with Vue2-Leaflet for map integration.
- Outlets data fetched from the Django REST API hosted on PythonAnywhere.
- Implemented a feature to display each outlet with a marker on the map.
- Added functionality to display a 5KM radius around each outlet.
- Implemented a toggle feature to show/hide the radius around outlets.
- Custom markers used for a better visual representation.

### Limitations and Solutions
- Map Loading Speed: To enhance performance, only necessary data is fetched and rendered.
- Responsive Design: Ensured the application is responsive to cater to different screen sizes.

## Deployment
Deployed on Netlify with continuous deployment from a GitHub repository.
Live application URL: Netlify App URL

### Deployment Process
- Connected the GitHub repository with Netlify.
- Set build settings in Netlify with `npm run build` as the build command and `dist/` as the publish directory.
- Added necessary environment variables in Netlify.

## Running the Project Locally
### Prerequisites
- Node.js and npm installed.
- Access to the backend API (running locally or via hosted URL).

### Step 1: Clone the Repository
Clone the frontend repository to your local machine:

```
git clone https://github.com/TahaKh99/mindhive_frontend.git
cd mindhive-frontend
```
Step 2: Install Dependencies
Install the required npm packages:
```
npm install
```
## Step 3: Set Up Environment Variables
Create a `.env.local` file in the root directory and add the following (replace with actual values):

```
VUE_APP_API_URL=http://127.0.0.1:8000/api/outlets/
VUE_APP_API_KEY=your_api_key_here
```

## Step 4: Run the Development Server
Start the Vue.js development server:
```

npm run serve
```

The server will start, and the application should be available at http://localhost:8080/.

## Step 5: Accessing the API
Ensure the backend API is accessible from the frontend application, either through a local Django server or a hosted PythonAnywhere URL.

## Conclusion
This documentation provides insights into the frontend development and deployment processes of the MindHive project. The application is designed to interact with a Django REST API, showcasing capabilities in frontend development, API integration, and deployment using modern web technologies.
