# NanoVNASaver Architecture

This document provides a high-level overview of the codebase framework for NanoVNASaver, detailing the entry point and the purpose of the primary subdirectories and files.

## Main Program Entry Point

The application execution starts in **`src/NanoVNASaver/__main__.py`**.
This file acts as the primary entry point:
- It uses `argparse` to process command-line arguments.
- Sets up logging for the application.
- Initializes the `PySide6` UI application.
- Instantiates and displays the main application window: `NanoVNASaver` (defined in `src/NanoVNASaver/NanoVNASaver.py`).

The **`src/NanoVNASaver/NanoVNASaver.py`** file contains the primary `NanoVNASaver` widget/window class. This class builds the main user interface, including the left-side control panels, right-side charts layout, handles UI events, and coordinates interactions between hardware sweeps, markers, and updates to the charts.

## Core Source Code (`src/NanoVNASaver/`)

The root `src/NanoVNASaver/` directory contains several important standalone modules:
- **`Calibration.py`**: Manages the VNA calibration states, handling Short-Open-Load-Through (SOLT) standard corrections and applying calibration math.
- **`SweepWorker.py`**: A background thread worker that handles the fetching of sweep data from the hardware to avoid freezing the main UI thread.
- **`Touchstone.py`**: Implements reading, parsing, and writing of 1-port and 2-port Touchstone files (`.s1p`, `.s2p`).
- **`Defaults.py`**: Application configuration classes and default settings management.
- **`Formatting.py` / `SITools.py`**: String formatting helpers to neatly display RF quantities (like Hz, Ohms) with proper SI prefixes.
- **`RFTools.py` / `AnalyticTools.py`**: Various RF engineering mathematical helper functions.

## Subdirectories

### `Analysis/`
This directory provides the mathematical and RF analysis routines that compute specific characteristics from the swept S-parameter data.
- **`Base.py`**: The base class for all analysis modules.
- **Filter Analysis (`LowPassAnalysis.py`, `HighPassAnalysis.py`, `BandPassAnalysis.py`, `BandStopAnalysis.py`)**: Tools for evaluating RF filter parameters like cutoff frequencies and bandwidths.
- **`AntennaAnalysis.py`, `EFHWAnalysis.py`**: Antenna-specific characteristics.
- **`ResonanceAnalysis.py`, `VSWRAnalysis.py`**: Identifies resonance points and computes VSWR metrics.
- **`PeakSearchAnalysis.py`**: Implements algorithms to locate peaks in the data.

### `Charts/`
Contains the implementation of all graph and chart types available to display in the main window UI.
- **`Chart.py`**: The base generic chart class.
- **Data Plots (`LogMag.py`, `Phase.py`, `VSWR.py`, `Polar.py`, `GroupDelay.py`, `Magnitude.py`, etc.)**: Individual classes representing standard RF plots.
- **Specialty Charts (`Smith.py`, `TDR.py`, `Capacitance.py`, `Inductance.py`, `Permeability.py`)**: Renders complex mappings like the Smith Chart, Time Domain Reflectometry, and components values derived from S-parameters.

### `Controls/`
Implements the UI widget blocks placed in the left-hand column of the main application window.
- **`Control.py`**: Base control widget.
- **`MarkerControl.py`**: Interface for adding, managing, and toggling data markers.
- **`SerialControl.py`**: Interface for discovering hardware ports and initiating a connection.
- **`SweepControl.py`**: Interface for entering frequency ranges (Start/Stop, Center/Span) and defining sweep segments.

### `Hardware/`
Manages all hardware communication interfaces and parsing of the raw serial data received from the VNA devices.
- **`VNA.py`, `Hardware.py`**: Abstract base classes defining standard VNA capabilities, limits, and command interfaces.
- **`Serial.py`**: Low-level serial port communication handling.
- **Device Implementations (`NanoVNA.py`, `NanoVNA_H4.py`, `NanoVNA_V2.py`, `TinySA.py`, `SV4401A.py`, etc.)**: Specific device classes that accommodate the differing command sets, baud rates, and protocol quirks for all supported hardware models.

### `Marker/`
Classes handling the logic and display of measurement markers on the charts.
- **`Widget.py`**: The standard marker readout display and tracking logic.
- **`Delta.py`**: Implements the Delta Marker logic (computing differences between two markers).
- **`Values.py`**: Data classes representing the state and coordinates of markers.

### `Settings/`
Configuration and data models that define specific measurement environments.
- **`Bands.py`**: Data structure to define and save customized frequency bands (like amateur radio bands) to highlight on charts.
- **`Sweep.py`**: Data structures defining sweep ranges and configurations.

### `Windows/`
Implementation of the auxiliary and popup dialog windows invoked from the main UI.
- **`CalibrationSettings.py`**: The UI for performing in-app Short/Open/Load calibration or loading calibration saves.
- **`DisplaySettings.py`**: The window handling color schemes, chart layout selections, and application appearance.
- **`TDR.py`**: The Time Domain Reflectometry settings dialog to configure propagation velocity and cable types.
- **Other Windows (`About.py`, `AnalysisWindow.py`, `SweepSettings.py`, `Files.py`, etc.)**: Corresponding pop-up dialog boxes for various other application menus.
- **`ui/`**: Usually contains Qt UI components and layouts.

### `utils/`
Helper modules that don't fit directly into the core domain logic.
- **`app_versions.py`, `version.py`**: Helpers to fetch and report the runtime environment details and application version info.
