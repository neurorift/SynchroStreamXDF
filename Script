import pyxdf
import pandas as pd
import os
import numpy as np
import matplotlib.pyplot as plt

def convert_xdf_to_csv(file_path, output_folder):
    # Initialize dictionaries and variables for storing dataframes and markers.
    dfs = {}  
    markers_df = None
    try:
        # Load data from the specified XDF file.
        data, header = pyxdf.load_xdf(file_path)
        print(f"Data successfully loaded from {file_path}")
        # Check if the data list is empty, which means no data streams were found.
        if not data:
            print("Warning: No data streams found in the file.")
            return dfs, markers_df

        # Process each data stream found in the file.
        for index, stream in enumerate(data):
            info = stream['info']
            # Extract the stream type, handling cases where type may not be provided.
            stream_type = info['type'][0] if 'type' in info else 'Unknown type'
            # Generate a valid filename-friendly stream name based on provided or default values.
            stream_name = info['name'][0].replace(" ", "_").replace("-", "_") if 'name' in info else f"Unnamed_stream_{index}"
            print(f"Processing stream {index + 1}: {stream_name} ({stream_type})")

            # Separate handling for marker streams to create a markers dataframe.
            if 'Markers' in stream_type:
                df = pd.DataFrame(stream['time_series'], columns=['Marker'])
                df['Timestamp'] = stream['time_stamps']
                markers_df = df
                print(f"Markers DataFrame created for {stream_name} with {len(df)} entries.")
            else:
                # Default channel labels if not specified in the stream info.
                channel_labels = ['Unknown']  
                # Extract channel labels from the stream info if available.
                if 'channel' in info['desc'][0]['channels'][0]:
                    channel_info = info['desc'][0]['channels'][0]['channel']
                    if isinstance(channel_info, list):
                        channel_labels = [ch["label"][0] if 'label' in ch else 'Unknown' for ch in channel_info]

                # Specific case handling for ECG data to ensure proper channel labeling.
                if 'ECG' in stream_type and all(label == 'Unknown' for label in channel_labels):
                    channel_labels = ['ECG']

                # Create a dataframe for the data stream using extracted channel labels and timestamps.
                df = pd.DataFrame(stream['time_series'], columns=channel_labels)
                df['Timestamp'] = stream['time_stamps']
                dfs[stream_name] = df
                print(f"Data DataFrame created for {stream_name} with {len(df)} entries and columns: {channel_labels}.")

    except Exception as e:
        print(f"An error occurred while loading {file_path}: {e}")
        return dfs, markers_df

    # Merge data streams with markers and save to CSV, if markers are present.
    if markers_df is not None:
        for name, df in dfs.items():
            # Merge data stream with markers using nearest timestamp matching.
            merged_df = pd.merge_asof(df.sort_values('Timestamp'), markers_df.sort_values('Timestamp'), on='Timestamp', direction='nearest')
            output_csv_path = os.path.join(output_folder, f"{name}_with_Markers.csv")
            merged_df.to_csv(output_csv_path, sep=';', encoding='utf-8', index=False)
            print(f"{name} data merged with markers and saved to {output_csv_path}")

    return dfs, markers_df

def combined_plot(dfs, markers_df, output_folder, y_limits=None):
    plt.figure(figsize=(15, 4 * len(dfs)))  # Increased vertical size for better clarity
    tobii_channels = {
        'left_pupil_diameter': 'pink',
        'right_pupil_diameter': 'purple',
        'left_gaze_point_on_display_area_0': 'green',
        'left_gaze_point_on_display_area_1': 'lightgreen',
        'right_gaze_point_on_display_area_0': 'blue',
        'right_gaze_point_on_display_area_1': 'lightblue',
    }
    y_limits = y_limits or {}

    for i, (name, df) in enumerate(dfs.items(), start=1):
        ax = plt.subplot(len(dfs), 1, i)
        merged_df = pd.merge_asof(df.sort_values('Timestamp'), markers_df.sort_values('Timestamp'), on='Timestamp', direction='nearest')

        if 'Tobii' in name:
            # Plot only specified Tobii channels
            for column in tobii_channels.keys():
                if column in df.columns:
                    ax.plot(merged_df['Timestamp'], merged_df[column], label=f'{column}', color=tobii_channels[column])
        else:
            ax.plot(merged_df['Timestamp'], merged_df[df.columns[0]], label=f'{name} Data', color='black')

        ax.scatter(markers_df['Timestamp'], [merged_df[df.columns[0]].min()]*len(markers_df), color='red', label='Markers', alpha=0.7)
        ax.set_title(f'Synchronization check for {name}', fontsize=14)
        ax.set_xlabel('Time (s)', fontsize=12)
        ax.set_ylabel('Amplitude', fontsize=12)
        ax.grid(True)

        if name in y_limits:
            ax.set_ylim(y_limits[name])

        # Only show x-axis labels on the bottom subplot
        if i < len(dfs):
            ax.set_xlabel('')

        # Enlarge tick parameters
        ax.tick_params(axis='both', which='major', labelsize=10)

        # Place the legend outside of the plot
        ax.legend(loc='upper right', bbox_to_anchor=(1.15, 1))

    plt.tight_layout(rect=[0, 0, 0.85, 1])  # Adjust layout to fit the outside legend
    plot_filename = os.path.join(output_folder, "combined_sync_check.png")
    plt.savefig(plot_filename)
    plt.close()  # Close the figure to free memory
    print(f"Combined plot saved to {plot_filename}")

def single_plot(dfs, markers_df, output_folder, y_limits=None):
    # Ensure the output directory exists
    if not os.path.exists(output_folder):
        os.makedirs(output_folder)
        print(f"Directory created: {output_folder}")
    else:
        print(f"Directory already exists: {output_folder}")

    tobii_channels = {
        'left_pupil_diameter': 'pink',
        'right_pupil_diameter': 'purple',
        'left_gaze_point_on_display_area_0': 'green',
        'left_gaze_point_on_display_area_1': 'lightgreen',
        'right_gaze_point_on_display_area_0': 'blue',
        'right_gaze_point_on_display_area_1': 'lightblue',
    }
    y_limits = y_limits or {}

    for name, df in dfs.items():
        plt.figure(figsize=(12, 6))  # Setting a standard figure size for all plots
        ax = plt.gca()
        merged_df = pd.merge_asof(df.sort_values('Timestamp'), markers_df.sort_values('Timestamp'), on='Timestamp', direction='nearest')

        if 'Tobii' in name:
            for column in df.columns:
                if column in tobii_channels:
                    ax.plot(merged_df['Timestamp'], merged_df[column], label=f'{column}', color=tobii_channels[column])
        else:
            ax.plot(merged_df['Timestamp'], merged_df[df.columns[0]], label=f'{name} Data', color='black')

        ax.scatter(markers_df['Timestamp'], [merged_df[df.columns[0]].min()]*len(markers_df), color='red', label='Markers', alpha=0.7)
        ax.set_title(f'Synchronization check for {name}')
        ax.set_xlabel('Time (s)')
        ax.set_ylabel('Amplitude')
        ax.legend()
        ax.grid(True)

        if name in y_limits:
            ax.set_ylim(y_limits[name])

        plot_filename = os.path.join(output_folder, f"{name}_sync_check.png")
        plt.savefig(plot_filename)
        plt.close()  # Close the figure to free memory
        print(f"{name} plot saved to {plot_filename}")

if __name__ == "__main__":
    file_path = '/path/to/xdf/file/as/in/the/following/example/sub-001_ses-S001_task-Default_run-001_eeg.xdf'
    output_folder = '/path/to/output/folder'
    
    # Execute conversion and get dataframes
    dfs, markers_df = convert_xdf_to_csv(file_path, output_folder)
    
    # Define y-axis limits for each stream if needed
    y_limits = {
        'Polar_H10_D818432F': (-2000, 2000),  # Adjust according to the actual ECG data range
        'Tobii': (-5, 5)  # Generic limits for Tobii, specify further if needed
    }

    # Call combined_plot if dataframes are properly loaded
    if dfs and markers_df is not None:
        combined_plot(dfs, markers_df, output_folder, y_limits)
        # Optionally call single_plot to generate individual plots for each stream
        single_plot(dfs, markers_df, output_folder, y_limits)
    else:
        print("Failed to load data or markers.")




