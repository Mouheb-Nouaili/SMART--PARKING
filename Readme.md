# Welcome to Smart Parking!
## Project Description

This project is a **Smart Parking System** using an **ESP32 board**, **ultrasonic sensors**, **servo motors**, an **LCD display**, and a **Node-RED interface**. The goal is to automate parking spot management, control vehicle entry/exit, and allow **online reservation and payment** through a web interface.


## Hardware Used

-   1x **ESP32 board**
    
-   2x **Servo motors** (for entry and exit barriers)
    
-   5x **Ultrasonic sensors**:
    
    -   1 for the **entry barrier**
        
    -   1 for the **exit barrier**
        
    -   3 for detecting **availability of 3 parking spots**
        
-   1x **LCD Display** with I2C module to show parking status
    
-   **Wi-Fi** connection for MQTT communication
## Main Features

-   Ultrasonic sensors detect:
    
    -   Vehicles at the **entry and exit barriers**
        
    -   **Parking spot availability** (3 slots)
   - LCD screen shows:

        -   Number of **available spots**
-   The ESP32 sends data to a server using **MQTT**
    
-   This data is displayed and controlled using **Node-RED**
- Simple web dashboard to:


    
  -   **Check parking availability**
  - **Fill out a reservation form** including:

    -   Full name  
    -   Email
    -   Vehicle license plate
     -   Bank card information (for online payment)
    -   Password
   -   **Reserve a parking spot**
## Reservation Policy

-   Once a user **reserves a spot** through the app, it is marked as **occupied**, even if the car hasn't arrived yet.
    
-   If the user **cancels** without using the spot, they are still **charged for the reserved time**.

## Usage Scenarios
 ###  User with the App
-   Accesses the Node-RED interface
    
-   Reserves a spot
    
-   Provides personal info
    
-   The entry barrier opens if the reservation is valid
    
-   At exit, **payment is automatically charged** based on the time from **reservation** to departure
### User without the App
-   Can only enter if a **free spot** is available
    

    
-   **Payment is made at the exit**, based on the time spent inside the parking
-    Otherwise, **entry is denied automatically**

## Future Improvements
### Add Image Recognition Cameras
To enhance automation, the system will integrate **ANPR (Automatic Number Plate Recognition)** technology at entry and exit to detect license plates.
-   At **entry**: verify reservation.
    
-   At **exit**: calculate parking time and **auto-charge** the saved card.
### Hardware & Software Options
-   **ALPR Camera**
    
-   **ANPR Camera** (_Automatic Number Plate Recognition_)
    
-   **LPR Camera** (License Plate Recognition)
-   Use **ESP32-CAM** for low-cost image capture.
    
-   Use **OpenCV + EasyOCR** or **OpenALPR** (Python, open-source) for plate detection.





# Conclusion

This Smart Parking system offers an efficient, automated solution for managing limited parking spaces. With real-time monitoring, online reservations, automatic payment, and future plans for license plate recognition, it enhances user convenience and optimizes parking operations. As the system evolves, it will become even smarter, faster, and more user-friendly — bringing us closer to fully automated urban parking.



