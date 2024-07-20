# SynchroStreamXDF

Welcome to the SynchroStreamXDF repository, a specialized tool designed for researchers and academics in neurophysiology and psychophysiology. This Python script facilitates the processing of XDF (Extensible Data Format) files, commonly used in neuroscientific research, to convert data into CSV format, integrate event markers, and generate comprehensive visualizations for data verification and analysis.

# Features

Data Conversion: Converts XDF files into CSV format for easy access and manipulation.
Event Marker Integration: Merges event markers with physiological data streams, crucial for analyzing stimuli-response experiments.
Data Visualization: Generates plots that overlay data streams with event markers to visually verify synchronization and integrity.
Adaptive Data Handling: Automatically adapts to various data types within XDF files, ensuring broad compatibility.
Robust Error Handling: Includes detailed error logging to aid in troubleshooting during data processing.
Getting Started

# Prerequisites
Ensure you have Python 3.x installed along with the following required libraries:

pyxdf
pandas
numpy
matplotlib
These can be installed using the following command:

pip install pyxdf pandas numpy matplotlib

# Installation
Clone the repository to your local machine:

git clone https://github.com/neurorift/SynchroStreamXDF.git

# Navigate to the repository directory:
cd SynchroStreamXDF

# Usage
To use SynchroStreamXDF, run the script from the command line, specifying the path to your XDF file and the desired output directory for CSV and visual outputs:

python synchrostreamxdf.py --file path_to_xdf_file --output output_directory

# Citation

When using SynchroStreamXDF in your research or other related work, please cite this repository as follows:

Tekampe, D. L. (2024). SynchroStreamXDF. GitHub repository, Neurorift. https://github.com/neurorift/SynchroStreamXDF

# Contributing

Contributions are what make the open-source community such an incredible place to learn, inspire, and create. Any contributions you make are greatly appreciated.

# Fork the Project
Create your Feature Branch (git checkout -b feature/AmazingFeature)
Commit your Changes (git commit -m 'Add some AmazingFeature')
Push to the Branch (git push origin feature/AmazingFeature)
Open a Pull Request

# License

This project is licensed under the MIT License - see the LICENSE.md file for details.

# Contact

Project Link: https://github.com/neurorift/SynchroStreamXDF
