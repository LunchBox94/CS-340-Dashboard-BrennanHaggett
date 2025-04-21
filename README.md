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


## Files

animal_shelter_BrennanHaggett.py
[Uploading afrom pymongo import MongoClient  # Import the MongoDB client from the PyMongo library

# Define class to control CRUD operations for the Animal collection in MongoDB
class AnimalShelter(object):
    """ CRUD operations for Animal collection in MongoDB """

    def __init__(self, username='root', password='M97RHKRnC9'):
        # Initializing the MongoClient and connecting to the MongoDB instance
        self.client = MongoClient(
            f'mongodb://{username}:{password}@nv-desktop-services.apporto.com:30958/?authSource=admin'
        )
        # Connect to the 'aac' database
        self.database = self.client['aac']
        # Connect to the 'animals' collection within the 'aac' database
        self.collection = self.database['animals']

    # Create: Insert a document into the collection
    def create(self, data):
        if data:
            try:
                result = self.collection.insert_one(data)
                return result.acknowledged
            except Exception as e:
                print("Insert failed:", e)
                return False
        else:
            raise Exception("No data provided to insert.")

    # Read: Query documents from the collection
    def read(self, criteria=None):
        try:
            if criteria:
                # Return documents that match the given criteria
                return list(self.collection.find(criteria, {"_id": False}))
            else:
                # If no criteria provided, return all documents
                return list(self.collection.find({}, {"_id": False}))
        except Exception as e:
            print("Read failed:", e)
            return []

    # Update: Modify documents based on criteria
    def update(self, criteria, updated_data):
        if criteria and updated_data:
            try:
                result = self.collection.update_many(criteria, {"$set": updated_data})
                return result.modified_count  # Return number of documents updated
            except Exception as e:
                print("Update failed:", e)
                return 0
        else:
            raise Exception("Both criteria and updated_data must be provided.")

    # Delete: Remove documents from the collection
    def delete(self, criteria):
        if criteria:
            try:
                result = self.collection.delete_many(criteria)
                return result.deleted_count  # Return number of documents deleted
            except Exception as e:
                print("Delete failed:", e)
                return 0
        else:
            raise Exception("No criteria provided to delete.")
nimal_shelter_BrennanHaggett.py…]()



## ProjectTwoDashboard.ipynb

