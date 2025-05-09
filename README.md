# CS-340 Dashboard - Brennan Haggett

This project is an interactive dashboard built with Python, Dash, and MongoDB. It enables the visualization and filtering of animal shelter data for Grazioso Salvare.

![Grazioso Salvare Logo](https://github.com/user-attachments/assets/5a80467f-d0c3-4aa3-8050-6ec16e88c166)

## Features

- Dynamic geolocation mapping of animal rescues
- Interactive pie chart showing breed distribution
- Filtering by rescue type (Water, Mountain, Disaster, Tracking)
- MongoDB integration with CRUD functionality

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


Project Summary –

For this project, I developed a full-stack web application dashboard for Grazioso Salvare using MongoDB, Dash, and Python. The project began by importing and preparing the aac_shelter_outcomes.csv data into MongoDB. I used mongoimport to load the data into the aac.animals collection and verified successful upload through the shell.

I then created a custom Python module, animal_shelter_BrennanHaggett.py, to implement the CRUD operations needed to interact with the database. Using this module, I read animal data into a Pandas DataFrame and built a dynamic dashboard using Dash within a Jupyter Notebook.

The dashboard includes radio buttons to filter data by rescue type (Water Rescue, Mountain/Wilderness, Disaster Rescue, and Individual Tracking). The interface updates a live DataTable, a pie chart showing breed distribution, and an interactive map showing the animal’s shelter location.

I embedded the Grazioso Salvare logo, used dash_leaflet for mapping, and plotly.express for the pie chart. The application is fully functional and responsive to user input. After testing all components and collecting screenshots, I uploaded the dashboard code, CRUD module, and README to GitHub for open-source availability.

One challenge was connecting to MongoDB using mongoimport, which initially failed due to incorrect credentials and connection strings. This was resolved by confirming the correct URI format with authSource = admin. Another issue occurred when attempting to drop the _id column, which was not always present in the DataFrame; adding a conditional check resolved this. Additional difficulties involved uploading files to the correct Apporto directory and ensuring paths were accurate. Debugging Dash callbacks for filtering and maps required careful inspection of inputs and outputs, which was resolved through testing and using print statements for verification.


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


## ProjectTwoDashboard.ipynb

# Import JupyterDash for running Dash apps in Jupyter notebooks
from jupyter_dash import JupyterDash

# Import core Dash components and libraries for layout and interactivity
import dash_leaflet as dl
from dash import dcc, html, dash_table
from dash.dependencies import Input, Output, State
import plotly.express as px
import base64
import os

# Import standard Python data analysis libraries
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# Import the custom MongoDB CRUD module
from animal_shelter_BrennanHaggett import AnimalShelter

#################################
# MongoDB Connection & Data Load
#################################
username = "root"
password = "M97RHKRnC9"

# Instantiate the AnimalShelter class for MongoDB access
db = AnimalShelter(username, password)

# Load the entire animal dataset into a pandas DataFrame
df = pd.DataFrame.from_records(db.read({}))

######################
# Dash App Setup
######################

# Initialize the Dash app
app = JupyterDash(__name__)

# Load and encode the Grazioso Salvare logo for display
image_filename = 'Grazioso Salvare Logo.png'
encoded_image = base64.b64encode(open(image_filename, 'rb').read())

# Define layout of the dashboard
app.layout = html.Div([
    # Display the organization logo
    html.Img(src='data:image/png;base64,{}'.format(encoded_image.decode()), style={'height': '100px'}),

    # Title
    html.Center(html.B(html.H1('CS-340 Dashboard - Brennan Haggett'))),

    html.Hr(),

    # Filter controls for selecting rescue type
    html.Div([
        html.Label("Select Rescue Type"),
        dcc.RadioItems(
            id='filter-type',
            options=[
                {'label': 'Water Rescue', 'value': 'Water Rescue'},
                {'label': 'Mountain/Wilderness Rescue', 'value': 'Mountain or Wilderness Rescue'},
                {'label': 'Disaster Rescue', 'value': 'Disaster Rescue'},
                {'label': 'Individual Tracking', 'value': 'Individual Tracking'},
                {'label': 'Reset', 'value': 'Reset'}
            ],
            value='Reset',
            inline=True
        )
    ]),

    html.Hr(),

    # Interactive data table to show query results
    dash_table.DataTable(
        id='datatable-id',
        columns=[{"name": i, "id": i, "deletable": False, "selectable": True} for i in df.columns],
        data=df.to_dict('records'),
        page_size=10,
        style_table={'overflowX': 'auto'},
        style_cell={'textAlign': 'left'},
        style_data_conditional=[
            {
                'if': {'column_id': i},
                'backgroundColor': '#f4f4f4',
                'fontWeight': 'bold'
            } for i in df.columns
        ],
    ),

    html.Br(),
    html.Hr(),

    # Two-column layout for charts and map
    html.Div(className='row', style={'display': 'flex'}, children=[
        html.Div(id='graph-id', className='col s12 m6'),
        html.Div(id='map-id', className='col s12 m6')
    ])
])

######################################
# Callback Functions - Interactivity
######################################

# Filter MongoDB results based on user-selected rescue type
@app.callback(
    Output('datatable-id', 'data'),
    [Input('filter-type', 'value')]
)
def update_dashboard(filter_type):
    query = {}

    if filter_type == "Water Rescue":
        query = {
            "animal_type": "Dog",
            "breed": {"$in": ["Labrador Retriever Mix", "Chesapeake Bay Retriever", "Newfoundland"]},
            "sex_upon_outcome": "Intact Female",
            "age_upon_outcome_in_weeks": {"$gte": 26, "$lte": 156}
        }
    elif filter_type == "Mountain or Wilderness Rescue":
        query = {
            "animal_type": "Dog",
            "breed": {"$in": ["German Shepherd", "Alaskan Malamute", "Old English Sheepdog", "Siberian Husky", "Rottweiler"]},
            "sex_upon_outcome": "Intact Male",
            "age_upon_outcome_in_weeks": {"$gte": 26, "$lte": 156}
        }
    elif filter_type == "Disaster or Individual Tracking":
        query = {
            "animal_type": "Dog",
            "breed": {"$in": ["Doberman Pinscher", "German Shepherd", "Golden Retriever", "Bloodhound", "Rottweiler"]},
            "sex_upon_outcome": "Intact Male",
            "age_upon_outcome_in_weeks": {"$gte": 20, "$lte": 300}
        }
    # Reset option shows all records
    elif filter_type == "Reset":
        query = {}

    # Query the database using the filter
    return db.read(query)

# Display pie chart showing breed distribution of filtered results
@app.callback(
    Output('graph-id', "children"),
    [Input('datatable-id', "derived_virtual_data")]
)
def update_graphs(viewData):
    if viewData is None or len(viewData) == 0:
        return html.Div([html.P("No data to display.")])
    
    dff = pd.DataFrame.from_dict(viewData)
    fig = px.pie(dff, names='breed', title='Breed Distribution')
    return [dcc.Graph(figure=fig)]

# Highlight selected column in the data table
@app.callback(
    Output('datatable-id', 'style_data_conditional'),
    [Input('datatable-id', 'selected_columns')]
)
def update_styles(selected_columns):
    if selected_columns is None:
        return []
    return [{
        'if': {'column_id': i},
        'background_color': '#D2F3FF'
    } for i in selected_columns]

# Display a marker on the map for the selected animal
@app.callback(
    Output('map-id', "children"),
    [Input('datatable-id', "derived_virtual_data"),
     Input('datatable-id', "derived_virtual_selected_rows")])
def update_map(viewData, index):
    if viewData is None or index is None:
        return []

    dff = pd.DataFrame.from_dict(viewData)
    row = index[0] if index else 0

    return [
        dl.Map(style={'width': '1000px', 'height': '500px'}, center=[30.75, -97.48], zoom=10, children=[
            dl.TileLayer(id="base-layer-id"),
            dl.Marker(position=[dff.iloc[row, 13], dff.iloc[row, 14]], children=[
                dl.Tooltip(dff.iloc[row, 4]),  # Tooltip showing breed
                dl.Popup([
                    html.H1("Animal Name"),
                    html.P(dff.iloc[row, 9])    # Popup showing animal name
                ])
            ])
        ])
    ]

# Launch the Dash server
app.run_server(debug=True)



# 8-2 Journal: Portfolio Item

# How do you write programs that are maintainable, readable, and adaptable?

I wrote programs that are maintainable, readable, and adaptable by using modular design, clear comments, and descriptive function names. In Project One, I built a CRUD Python module that handled all database operations separately from the dashboard code. This made it easy to connect the database to the dashboard widgets in Project Two without rewriting functions. The advantage of working this way was that if any part of the database interaction needed to change, I could update the CRUD module without touching the dashboard’s interface code. In the future, I could reuse this CRUD module in different projects by simply adjusting the database connection details, saving time and ensuring consistency.


# How do you approach a problem as a computer scientist?

I approach problems by breaking them down into smaller, manageable steps. For the Grazioso Salvare project, I first analyzed the database and dashboard requirements separately. I designed how the CRUD module would interact with the database and then built the dashboard on top of that solid foundation. This approach differed from previous courses where I might have jumped into coding without fully planning. In the future, I will continue focusing on strong upfront design, clear database schema planning, and modular coding to meet new client requests efficiently.



# What do computer scientists do, and why does it matter?

Computer scientists solve real-world problems by designing, building, and maintaining systems that make information easy to access and use. In this project, my work would help a company like Grazioso Salvare manage their animal tracking data more efficiently, saving time and reducing human errors. By building adaptable, user-friendly tools like dashboards connected to databases, computer scientists enable companies to make better decisions, operate more smoothly, and ultimately achieve their goals.

