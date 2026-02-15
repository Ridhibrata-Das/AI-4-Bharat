# Requirements Document: E-Bhoomi Agricultural IoT Dashboard

## Introduction

E-Bhoomi is a comprehensive agricultural IoT dashboard application that provides real-time monitoring, AI-powered recommendations, and automated irrigation control for precision farming. The system integrates multiple IoT sensors, weather data, and machine learning models to help farmers make data-driven decisions and optimize crop management.

## Glossary

- **System**: The E-Bhoomi Agricultural IoT Dashboard application
- **ThingSpeak**: IoT data aggregation platform for sensor data collection
- **Sensor_Data**: Real-time measurements from IoT devices (soil moisture, temperature, humidity, NPK)
- **NPK**: Nitrogen, Phosphorus, and Potassium nutrient levels in soil
- **Pump_Controller**: MQTT-based irrigation pump control system
- **Balaram_AI**: Google Gemini-powered AI chatbot with voice and camera capabilities
- **ML_Service**: Machine learning recommendation service for agricultural actions
- **Alert_System**: SMS notification system via Twilio for critical conditions
- **Weather_Service**: OpenMeteo API integration for weather forecasting
- **Dashboard_User**: Farmer or agricultural professional using the system
- **Time_Range**: Historical data viewing period (1h, 24h, 7d, 30d, 1y)

## Requirements

### Requirement 1: Real-Time IoT Sensor Monitoring

**User Story:** As a farmer, I want to monitor real-time sensor data from my farm, so that I can track environmental conditions and soil health continuously.

#### Acceptance Criteria

1. WHEN the dashboard loads, THE System SHALL fetch and display current sensor readings from ThingSpeak
2. THE System SHALL display soil moisture percentage with status indicators (Good: 40-60%, Moderate: 20-40% or 60-80%, Danger: <20% or >80%)
3. THE System SHALL display temperature readings in Celsius from weather sensors
4. THE System SHALL display humidity percentage from environmental sensors
5. THE System SHALL display NPK (Nitrogen, Phosphorus, Potassium) levels in parts per million (ppm)
6. WHEN sensor data is updated, THE System SHALL show trend indicators (increasing/decreasing) with percentage change
7. THE System SHALL poll ThingSpeak API at intervals based on selected time range (15s for 1h, 1min for 24h, 5min for 7d, 15min for 30d, 1h for 1y)
8. WHEN ThingSpeak API is unavailable, THE System SHALL display error messages and retry with exponential backoff
9. THE System SHALL cache sensor data for 30 seconds to reduce API calls

### Requirement 2: Historical Data Visualization

**User Story:** As a farmer, I want to view historical sensor data across different time ranges, so that I can analyze trends and patterns in my farm's environmental conditions.

#### Acceptance Criteria

1. THE System SHALL provide time range selection options (Last Hour, Last 24 Hours, Last 7 Days, Last 30 Days, Last Year)
2. WHEN a time range is selected, THE System SHALL fetch appropriate number of data points (60 for 1h, 144 for 24h, 168 for 7d, 720 for 30d, 8760 for 1y)
3. THE System SHALL display line charts for temperature history with time on X-axis and temperature in °C on Y-axis
4. THE System SHALL display line charts for humidity history with time on X-axis and humidity percentage on Y-axis
5. THE System SHALL display line charts for soil moisture history with time on X-axis and moisture percentage on Y-axis
6. THE System SHALL display multi-line charts for NPK data showing Nitrogen, Phosphorus, and Potassium on the same graph
7. THE System SHALL provide interactive chart features including zoom, pan, and brush selection
8. THE System SHALL format time labels appropriately for each time range (HH:MM for short ranges, dates for longer ranges)
9. WHEN chart data is loading, THE System SHALL display loading indicators

### Requirement 3: Automated Irrigation Control

**User Story:** As a farmer, I want to control my irrigation pump manually or automatically, so that I can optimize water usage and prevent over-watering or under-watering.

#### Acceptance Criteria

1. THE System SHALL provide manual and automatic pump control modes via toggle switch
2. WHEN in manual mode, THE Dashboard_User SHALL be able to turn the pump ON or OFF via button interface
3. WHEN in automatic mode, THE System SHALL turn the pump ON when soil moisture drops to or below 10%
4. WHEN in automatic mode, THE System SHALL turn the pump OFF when soil moisture reaches or exceeds 80%
5. WHEN in manual mode AND pump is ON AND soil moisture reaches 80%, THE System SHALL automatically turn the pump OFF (auto-cutoff safety feature)
6. THE System SHALL publish pump commands to MQTT broker using configured topic (default: pumps/field1/cmd)
7. THE System SHALL use MQTT over TLS (mqtts protocol) with authentication credentials
8. THE System SHALL display current pump state (ON, OFF, or unknown) with visual indicators
9. WHEN pump command fails, THE System SHALL display error notification to Dashboard_User
10. THE System SHALL check pump state and soil moisture every 15 seconds in automatic mode

### Requirement 4: SMS Alert System

**User Story:** As a farmer, I want to receive SMS alerts for critical soil moisture conditions, so that I can take immediate action when my crops are at risk.

#### Acceptance Criteria

1. WHEN soil moisture exceeds 80%, THE System SHALL send SMS alert "Plant is drowning, please remove water" via Twilio
2. WHEN soil moisture drops below 20%, THE System SHALL send SMS alert "Please water the plants" via Twilio
3. THE System SHALL send SMS to configured phone number from environment variables
4. THE System SHALL display toast notification to Dashboard_User when alert is triggered
5. WHEN Twilio API call fails, THE System SHALL log error and display failure notification
6. THE System SHALL use Twilio REST API with Basic authentication
7. THE System SHALL include timestamp and sensor reading in alert context

### Requirement 5: AI-Powered Agricultural Recommendations

**User Story:** As a farmer, I want to receive AI-powered recommendations based on my sensor data, so that I can make informed decisions about fertilization, irrigation, and pest control.

#### Acceptance Criteria

1. THE System SHALL fetch latest sensor data (N, P, K, temperature, humidity, soil moisture) from ThingSpeak
2. THE System SHALL send sensor data to ML_Service for recommendation analysis
3. THE System SHALL display recommended action (Apply Fertilizer, Apply Pesticide, Irrigate, Monitor)
4. THE System SHALL display semantic tag describing the agricultural condition
5. THE System SHALL display NDI (Nitrogen Deficiency Index) and PDI (Phosphorus Deficiency Index) labels
6. THE System SHALL display confidence score as percentage (0-100%)
7. THE System SHALL display urgency level (urgent, moderate, good) with color coding (red, yellow, green)
8. THE System SHALL show action-specific icons (🌱 for fertilizer, 🛡️ for pesticide, 💧 for irrigation, 👁️ for monitoring)
9. THE System SHALL refresh recommendations automatically every 5 minutes
10. THE Dashboard_User SHALL be able to manually refresh recommendations via button
11. WHEN ML_Service is unavailable, THE System SHALL display error message and continue showing last known recommendation

### Requirement 6: Balaram AI Chatbot Integration

**User Story:** As a farmer, I want to interact with an AI assistant using voice, camera, and text, so that I can get instant answers to my farming questions and analyze crop images.

#### Acceptance Criteria

1. THE System SHALL provide two interaction modes: Voice/Camera AI and Text Chatbot
2. THE System SHALL integrate Google Gemini AI for natural language processing
3. WHEN in Voice/Camera mode, THE System SHALL capture audio input from microphone
4. WHEN in Voice/Camera mode, THE System SHALL capture video frames from camera
5. THE System SHALL send audio and visual data to Google Gemini for multimodal analysis
6. THE System SHALL display AI responses with timestamps and sender identification
7. WHEN in Chatbot mode, THE System SHALL provide text input interface
8. THE System SHALL maintain conversation history with message threading
9. THE System SHALL display human messages with green styling and AI messages with blue styling
10. WHEN Gemini API key is missing or invalid, THE System SHALL display configuration error

### Requirement 7: Weather Integration and Forecasting

**User Story:** As a farmer, I want to view current weather conditions and 5-day forecasts, so that I can plan my farming activities around weather patterns.

#### Acceptance Criteria

1. THE System SHALL fetch weather data from OpenMeteo API based on GPS coordinates
2. THE System SHALL display current temperature in Celsius
3. THE System SHALL display current humidity percentage
4. THE System SHALL display wind speed in km/h
5. THE System SHALL display atmospheric pressure in hPa
6. THE System SHALL display visibility in kilometers
7. THE System SHALL display UV index (0-10 scale)
8. THE System SHALL display "feels like" temperature calculated from temperature, humidity, and wind speed
9. THE System SHALL provide 5-day weather forecast with daily high/low temperatures
10. THE System SHALL display weather conditions (Clear sky, Cloudy, Rain, etc.) based on weather codes
11. THE System SHALL display precipitation probability percentage for each forecast day
12. THE System SHALL cache weather data for 5 minutes to reduce API calls
13. THE Dashboard_User SHALL be able to detect current location via browser geolocation API
14. THE System SHALL use OpenCage API for reverse geocoding to display location names

### Requirement 8: Location Detection and Management

**User Story:** As a farmer, I want to detect my current location automatically, so that I can get accurate weather data and location-specific recommendations for my farm.

#### Acceptance Criteria

1. THE System SHALL request browser geolocation permission from Dashboard_User
2. WHEN location permission is granted, THE System SHALL retrieve GPS coordinates (latitude, longitude)
3. THE System SHALL display location accuracy in meters
4. THE System SHALL use high-accuracy GPS mode with 10-second timeout
5. THE System SHALL perform reverse geocoding using OpenCage API to get human-readable location name
6. THE System SHALL display location as "City, State, Country" format when available
7. WHEN reverse geocoding fails, THE System SHALL display coordinates in decimal degrees format
8. THE System SHALL validate coordinates are within valid ranges (latitude: -90 to 90, longitude: -180 to 180)
9. WHEN location detection fails, THE System SHALL display error message with failure reason
10. THE System SHALL fall back to default location (Kolkata, India: 22.5626, 88.363) when detection fails
11. THE System SHALL update weather data automatically when location changes

### Requirement 9: Omnidim AI Voice Call Integration

**User Story:** As a farmer, I want to schedule AI-powered voice calls, so that I can receive verbal updates and interact with the system hands-free while working in the field.

#### Acceptance Criteria

1. THE System SHALL integrate with Omnidim AI calling service
2. THE Dashboard_User SHALL be able to initiate AI calls by entering a 10-digit phone number
3. THE System SHALL validate phone number format (exactly 10 digits)
4. THE System SHALL convert phone number to E.164 format (+91 prefix for India)
5. THE System SHALL send call request to Omnidim API with agent ID and phone number
6. THE System SHALL include call context (customer name, account ID, priority) in API request
7. THE System SHALL use Bearer token authentication for Omnidim API
8. WHEN call is successfully initiated, THE System SHALL display success notification
9. WHEN call initiation fails, THE System SHALL display error message with failure reason
10. THE System SHALL use configured Omnidim base URL, API key, agent ID, and from number ID from environment variables

### Requirement 10: Dashboard Navigation and Layout

**User Story:** As a farmer, I want to navigate between different dashboard sections easily, so that I can access all features without confusion.

#### Acceptance Criteria

1. THE System SHALL provide navigation menu with links to all dashboard sections
2. THE System SHALL include dashboard sections: Overview, Sensors, Alerts, Recommendations, Analytics, Weather, Balaram AI, My Farm, Drone, Settings
3. THE System SHALL highlight active navigation item
4. THE System SHALL use responsive layout that adapts to mobile, tablet, and desktop screens
5. THE System SHALL display gradient header with welcome message and current temperature
6. THE System SHALL show last update timestamp on dashboard
7. THE System SHALL provide quick action buttons for common tasks (Ask Balaram AI, View Sensors, View Alerts, Schedule Call)
8. THE System SHALL use consistent color scheme (green for agriculture, blue for water, red for alerts)
9. THE System SHALL display loading states during data fetching
10. THE System SHALL handle navigation without full page reloads (client-side routing)

### Requirement 11: Analytics and Performance Tracking

**User Story:** As a farmer, I want to view analytics about my farm's performance, so that I can track yield, resource usage, and identify optimization opportunities.

#### Acceptance Criteria

1. THE System SHALL display total crop yield in kilograms with trend percentage
2. THE System SHALL display average temperature over last 30 days
3. THE System SHALL display current soil moisture level
4. THE System SHALL track water usage in liters with monthly comparison
5. THE System SHALL track fertilizer costs in currency with monthly comparison
6. THE System SHALL track energy consumption in kWh with monthly comparison
7. THE System SHALL display performance trends with up/down indicators
8. THE System SHALL provide export functionality for analytics data
9. THE System SHALL display AI-generated insights and recommendations based on analytics
10. THE System SHALL categorize insights as optimization opportunities, growth trends, cost alerts, or efficiency improvements
11. THE System SHALL provide filter options for date ranges
12. THE System SHALL display placeholder charts for yield trends, resource usage, and environmental factors

### Requirement 12: Alert Configuration and Management

**User Story:** As a farmer, I want to configure alert thresholds and enable/disable specific alerts, so that I can customize notifications based on my crop requirements.

#### Acceptance Criteria

1. THE System SHALL provide alert configuration interface for multiple alert types
2. THE System SHALL support alert types: Temperature High, Temperature Low, Humidity High, Humidity Low, Soil Moisture Low
3. THE Dashboard_User SHALL be able to set custom threshold values for each alert type
4. THE Dashboard_User SHALL be able to enable or disable individual alerts via toggle switches
5. THE System SHALL validate threshold values are within sensor measurement ranges
6. THE System SHALL persist alert configuration across sessions
7. THE System SHALL provide "Test Alerts" functionality to verify alert system
8. THE System SHALL display current threshold value and enabled status for each alert
9. WHEN threshold is updated, THE System SHALL apply changes immediately to monitoring logic

### Requirement 13: Recommendation History and Statistics

**User Story:** As a farmer, I want to view historical recommendations and dataset statistics, so that I can track recommendation patterns and understand the ML model's behavior.

#### Acceptance Criteria

1. THE System SHALL maintain history of last 10 recommendations with timestamps
2. THE System SHALL display sensor data snapshot (N, P, K, temperature, humidity, soil moisture) for each historical recommendation
3. THE System SHALL display action, confidence score, and semantic tag for each historical recommendation
4. THE System SHALL fetch and display ML dataset statistics (total records, action types, semantic tags)
5. THE System SHALL show total number of records in ML training dataset
6. THE System SHALL show number of unique action types in dataset
7. THE System SHALL show number of unique semantic tags in dataset
8. THE System SHALL display statistics in categorized cards with color coding
9. THE System SHALL persist recommendation history in browser session storage

### Requirement 14: Multi-Sensor Data Aggregation

**User Story:** As a farmer, I want all my sensor data aggregated in one place, so that I can view comprehensive farm status at a glance.

#### Acceptance Criteria

1. THE System SHALL aggregate data from multiple ThingSpeak channel fields (field1-7)
2. THE System SHALL map field1 to soil moisture readings
3. THE System SHALL map field2 to temperature readings
4. THE System SHALL map field3 to humidity readings
5. THE System SHALL map field5 to nitrogen (N) readings
6. THE System SHALL map field6 to phosphorus (P) readings
7. THE System SHALL map field7 to potassium (K) readings
8. THE System SHALL parse numeric values from string fields with fallback to zero
9. THE System SHALL handle missing or invalid sensor data gracefully
10. THE System SHALL calculate derived metrics (NPK average, feels-like temperature)
11. THE System SHALL synchronize data fetching across all sensor types

### Requirement 15: Responsive Design and Mobile Support

**User Story:** As a farmer, I want to access the dashboard on my mobile phone, so that I can monitor my farm while working in the field.

#### Acceptance Criteria

1. THE System SHALL render correctly on mobile devices (320px minimum width)
2. THE System SHALL render correctly on tablets (768px minimum width)
3. THE System SHALL render correctly on desktop screens (1024px and above)
4. THE System SHALL use responsive grid layouts that adapt to screen size
5. THE System SHALL adjust font sizes for readability on small screens
6. THE System SHALL stack cards vertically on mobile devices
7. THE System SHALL provide touch-friendly button sizes (minimum 44x44px)
8. THE System SHALL optimize chart rendering for mobile viewports
9. THE System SHALL hide non-essential UI elements on small screens
10. THE System SHALL support both portrait and landscape orientations
11. THE System SHALL use mobile-optimized navigation (hamburger menu or bottom navigation)

### Requirement 16: Error Handling and User Feedback

**User Story:** As a farmer, I want clear error messages and feedback, so that I understand what's happening and can troubleshoot issues.

#### Acceptance Criteria

1. WHEN API calls fail, THE System SHALL display user-friendly error messages via toast notifications
2. THE System SHALL log detailed error information to browser console for debugging
3. THE System SHALL display loading spinners during asynchronous operations
4. THE System SHALL show success notifications for completed actions (pump control, location detection, etc.)
5. THE System SHALL display warning notifications for non-critical issues
6. THE System SHALL provide retry mechanisms for failed API calls
7. THE System SHALL handle network timeouts gracefully (10 seconds for ThingSpeak, 5 seconds for weather)
8. WHEN environment variables are missing, THE System SHALL display configuration error messages
9. THE System SHALL validate user inputs before sending to APIs
10. THE System SHALL display inline validation errors for form inputs

### Requirement 17: Data Persistence and Caching

**User Story:** As a farmer, I want the dashboard to load quickly and work efficiently, so that I can access information without delays.

#### Acceptance Criteria

1. THE System SHALL cache ThingSpeak API responses for 30 seconds using Next.js revalidation
2. THE System SHALL cache weather API responses for 5 minutes (300 seconds)
3. THE System SHALL store recommendation history in browser session storage
4. THE System SHALL store alert configuration in browser local storage
5. THE System SHALL implement stale-while-revalidate caching strategy for sensor data
6. THE System SHALL prefetch navigation routes for faster page transitions
7. THE System SHALL optimize image loading with lazy loading and responsive images
8. THE System SHALL minimize bundle size through code splitting
9. THE System SHALL use server-side rendering for initial page load performance

### Requirement 18: Security and Authentication

**User Story:** As a system administrator, I want secure API communication and credential management, so that sensitive farm data and control systems are protected.

#### Acceptance Criteria

1. THE System SHALL store all API keys and credentials in environment variables
2. THE System SHALL never expose API keys in client-side code or browser console
3. THE System SHALL use HTTPS for all external API communications
4. THE System SHALL use MQTT over TLS (mqtts) for pump control communications
5. THE System SHALL validate and sanitize all user inputs before processing
6. THE System SHALL implement CORS policies for API routes
7. THE System SHALL use secure authentication methods (Bearer tokens, Basic auth) for external services
8. THE System SHALL mask sensitive information in logs and error messages
9. THE System SHALL implement rate limiting for API endpoints to prevent abuse
10. THE System SHALL validate API response data before rendering to prevent XSS attacks

### Requirement 19: Configuration Management

**User Story:** As a system administrator, I want to configure the system through environment variables, so that I can deploy to different environments without code changes.

#### Acceptance Criteria

1. THE System SHALL read ThingSpeak channel ID from NEXT_PUBLIC_THINGSPEAK_CHANNEL_ID
2. THE System SHALL read ThingSpeak API key from NEXT_PUBLIC_THINGSPEAK_READ_API_KEY
3. THE System SHALL read Twilio credentials from TWILIO_ACCOUNT_SID, TWILIO_AUTH_TOKEN, TWILIO_TO_PHONE
4. THE System SHALL read MQTT configuration from MQTT_URL, MQTT_PORT, MQTT_USERNAME, MQTT_PASSWORD, MQTT_TOPIC
5. THE System SHALL read Google Gemini API key from NEXT_PUBLIC_GEMINI_API_KEY
6. THE System SHALL read OpenCage API key from NEXT_PUBLIC_OPENCAGE_API_KEY
7. THE System SHALL read Omnidim configuration from NEXT_PUBLIC_OMNIDIM_BASE_URL, NEXT_PUBLIC_OMNIDIM_API_KEY, NEXT_PUBLIC_OMNIDIM_AGENT_ID, NEXT_PUBLIC_OMNIDIM_FROM_NUMBER_ID
8. THE System SHALL read ML service URL from ML_SERVICE_URL
9. THE System SHALL provide default values for optional configuration parameters
10. WHEN required environment variables are missing, THE System SHALL display clear error messages indicating which variables are needed
11. THE System SHALL validate environment variable formats on application startup

### Requirement 20: Accessibility and Internationalization

**User Story:** As a farmer with accessibility needs, I want the dashboard to be usable with assistive technologies, so that I can access all features regardless of my abilities.

#### Acceptance Criteria

1. THE System SHALL provide semantic HTML structure with proper heading hierarchy
2. THE System SHALL include ARIA labels for interactive elements
3. THE System SHALL support keyboard navigation for all interactive features
4. THE System SHALL provide sufficient color contrast ratios (WCAG AA minimum)
5. THE System SHALL include alt text for all informational images
6. THE System SHALL support screen reader announcements for dynamic content updates
7. THE System SHALL provide focus indicators for keyboard navigation
8. THE System SHALL avoid relying solely on color to convey information
9. THE System SHALL support browser zoom up to 200% without breaking layout
10. THE System SHALL use next-i18next for internationalization support (configured but not fully implemented)
