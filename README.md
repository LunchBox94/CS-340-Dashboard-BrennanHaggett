# CS-340 Dashboard - Brennan Haggett

This project is an interactive dashboard built with Python, Dash, and MongoDB. It enables the visualization and filtering of animal shelter data for Grazioso Salvare.

![Grazioso Salvare Logo](https://github.com/user-attachments/assets/5a80467f-d0c3-4aa3-8050-6ec16e88c166)

## Features

- 📍 Dynamic geolocation mapping of animal rescues
- 📊 Interactive pie chart showing breed distribution
- 🔎 Filtering by rescue type (Water, Mountain, Disaster, Tracking)
- 🐾 MongoDB integration with CRUD functionality

## Technologies Used

- Python
- Dash by Plotly
- Dash Leaflet
- MongoDB
- Pandas
- Plotly Express

## Setup Instructions

-Make sure Python is installed. Then run:

-pip install pandas dash dash-leaflet pymongo plotly jupyter-dash

-Launch JupyterLab

-Open the .ipynb dashboard file in JupyterLab and run all cells.

-Ensure MongoDB connection

-The dashboard connects to MongoDB using credentials and the AAC database. Update credentials if needed.

-Run the Dash app

-The app will launch at http://127.0.0.1:8050/.


## Screenshot

![Dashboard Screenshot]

Reset (returns all widgets to their original, unfiltered state) - 
![Capture](https://github.com/user-attachments/assets/77a3bced-9026-4631-b407-cf78f44d27c9)

Disaster or Individual Tracking-
![Capture - disaster](https://github.com/user-attachments/assets/1b758da7-00e7-450c-9ef5-2bb20c86c53b)
![Capture - ind](https://github.com/user-attachments/assets/e630deaa-de90-498a-918c-83aa2ef7f436)

Mountain or Wilderness Rescue -
![Capture - mnt](https://github.com/user-attachments/assets/9934ae97-6e94-47c9-a752-0b05ee360e4d)

Water Rescue-
![Capture - water](https://github.com/user-attachments/assets/68ccc07a-5584-40e8-bbdf-551e6aadc5ef)
