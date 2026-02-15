# E-Bhoomi - Agricultural IoT Dashboard
## Requirements Document

### Project Overview
E-Bhoomi is an intelligent agricultural monitoring and management system that leverages IoT sensors, real-time data analytics, and AI-powered recommendations to help farmers optimize crop health, resource usage, and yield. The platform integrates multiple data sources including soil sensors, weather APIs, and NPK (Nitrogen, Phosphorus, Potassium) sensors to provide actionable insights.

### Target Users
- Small to medium-scale farmers
- Agricultural consultants
- Farm managers
- Agricultural researchers
- Agri-tech enthusiasts

---

## 1. Functional Requirements

### 1.1 Real-Time Sensor Monitoring
**Priority:** High

**User Story:** As a farmer, I want to monitor real-time sensor data from my farm so that I can make informed decisions about crop management.

**Acceptance Criteria:**
- System displays current readings for soil moisture, temperature, humidity, and NPK levels
- Data refreshes automatically at configurable intervals (15s to 1 hour based on time range)
- Visual indicators show sensor status (good, moderate, danger)
- Trend indicators show whether values are increasing or decreasing
- System handles sensor data from ThingSpeak IoT platform
- Error handling for missing or invalid sensor data

### 1.2 Historical Data Visualization
**Priority:** High

**User Story:** As a farmer, I want to view historical trends of sensor data so that I can understand patterns and make better decisions.

**Acceptance Criteria:**
- Interactive line charts display historical data for all sensor types
- Time range selection: 1 hour, 24 hours, 7 days, 30 days, 1 year
- Charts include zoom, pan, and brush features for detailed analysis
- Separate visualizations for temperature, humidity, soil moisture, and NPK levels
- Data points are properly formatted with timestamps
- Charts are responsive and work on mobile devices

### 1.3 Weather Integration
**Priority:** High

**User Story:** As a farmer, I want to see current weather conditions for my location so that I can plan farming activities accordingly.

**Acceptance Criteria:**
- System fetches weather data from Open-Meteo API
- Displays current temperature and humidity
- Location detection using browser geolocation API
- Manual location selection option
- Reverse geocoding to display location name
- Weather data updates every 5 minutes
- Fallback to default location if geolocation fails

### 1.4 AI-Powered Agriculture Recommendations
**Priority:** High

**User Story:** As a farmer, I want to receive AI-powered recommendations based on my sensor data so that I can take appropriate actions for crop health.

**Acceptance Criteria:**
- System analyzes NPK levels, soil moisture, temperature, and humidity
- Provides actionable recommendations: Apply Fertilizer, Apply Pesticide, Irrigate, Monitor
- Displays confidence score for each recommendation
- Shows semantic tags explaining the reasoning
- Includes NDI (Nitrogen Deficiency Index) and PDI (Phosphorus Deficiency Index) labels
- Color-coded urgency levels: urgent (red), moderate (yellow), good (green)
- Recommendations update automatically with new sensor data

### 1.5 Smart Irrigation Control
**Priority:** High

**User Story:** As a farmer, I want to control my irrigation pump automatically or manually based on soil moisture levels.

**Acceptance Criteria:**
- Manual mode: User can toggle pump on/off via UI
- Automatic mode: System controls pump based on soil moisture thresholds
  - Turns ON when soil moisture ≤ 10%
  - Turns OFF when soil moisture ≥ 80%
- Safety feature: Auto-shutoff at 80% moisture even in manual mode
- Visual indicator shows pump status (on/off)
- Mode toggle switch (Manual/Automatic)
- MQTT integration for real-time pump control
- Error handling for pump control failures

### 1.6 Alert System
**Priority:** Medium

**User Story:** As a farmer, I want to receive alerts when critical conditions are detected so that I can take immediate action.

**Acceptance Criteria:**
- SMS alerts via Twilio when soil moisture is critical (>80% or <20%)
- In-app toast notifications for all alerts
- Alert messages are contextual and actionable
- Alert history tracking
- Configurable alert thresholds (future enhancement)

### 1.7 AI Chatbot Assistant (Balaram AI)
**Priority:** Medium

**User Story:** As a farmer, I want to ask questions about my farm data and get intelligent responses from an AI assistant.

**Acceptance Criteria:**
- Text-based chat interface powered by Google Gemini AI
- Voice input support with speech-to-text
- Camera integration for visual crop analysis
- Context-aware responses based on current sensor data
- Conversation history maintained during session
- Support for agricultural queries in natural language
- Multilingual support (future enhancement)

### 1.8 Voice AI Integration
**Priority:** Low

**User Story:** As a farmer, I want to interact with the system using voice commands for hands-free operation.

**Acceptance Criteria:**
- Voice call initiation via Omnidim API
- Phone number input with validation (10-digit Indian numbers)
- Call context includes user information and priority
- Success/failure notifications for call initiation
- ElevenLabs ConvAI widget integration for voice interactions

### 1.9 Dashboard Overview
**Priority:** High

**User Story:** As a farmer, I want a comprehensive dashboard that shows all key metrics at a glance.

**Acceptance Criteria:**
- Welcome header with current temperature and location
- Key metrics cards: Temperature, Humidity, Soil Moisture, NPK Average, Time Range
- Action suggestion card with current AI recommendation
- Quick action buttons: Balaram AI, Sensors, Alerts, Schedule
- Multiple chart views: NPK trends, Temperature history, Humidity history
- Responsive layout for desktop, tablet, and mobile
- Loading states for all data fetching operations

### 1.10 Recommendation History
**Priority:** Medium

**User Story:** As a farmer, I want to view past recommendations so that I can track actions taken and their outcomes.

**Acceptance Criteria:**
- Display last 10 recommendations with timestamps
- Show sensor data context for each recommendation
- Color-coded by urgency level
- Includes confidence scores and action types
- Filterable and searchable (future enhancement)
- Export functionality (future enhancement)

---

## 2. Non-Functional Requirements

### 2.1 Performance
- Dashboard loads within 3 seconds on 4G connection
- Sensor data updates with <2 second latency
- Charts render smoothly with up to 8760 data points (1 year)
- API responses cached appropriately (30s for sensor data, 5min for weather)
- Optimistic UI updates for pump control

### 2.2 Reliability
- 99% uptime for core monitoring features
- Graceful degradation when external APIs are unavailable
- Automatic retry logic for failed API calls
- Error boundaries to prevent full app crashes
- Data validation for all sensor inputs

### 2.3 Scalability
- Support for multiple farms per user (future)
- Handle up to 1000 concurrent users
- Efficient data polling with configurable intervals
- Lazy loading for historical data
- Pagination for large datasets

### 2.4 Security
- Environment variables for all API keys
- HTTPS for all API communications
- Input validation and sanitization
- Rate limiting for API endpoints
- Secure storage of user credentials (future)

### 2.5 Usability
- Intuitive navigation with clear menu structure
- Consistent color coding for status indicators
- Responsive design for all screen sizes
- Accessible UI components (WCAG 2.1 Level AA target)
- Loading states and error messages
- Tooltips and help text where needed

### 2.6 Compatibility
- Modern browsers: Chrome, Firefox, Safari, Edge (latest 2 versions)
- Mobile browsers: iOS Safari, Chrome Mobile
- Screen sizes: 320px to 4K displays
- Touch and mouse input support

### 2.7 Maintainability
- TypeScript for type safety
- Component-based architecture with React
- Consistent code style with ESLint
- Modular service layer for API integrations
- Comprehensive error logging
- Documentation for all major functions

---

## 3. Technical Requirements

### 3.1 Frontend Stack
- **Framework:** Next.js 13.5.1 (React 18.2.0)
- **Language:** TypeScript 5.2.2
- **Styling:** Tailwind CSS 3.3.3
- **UI Components:** Radix UI primitives
- **Charts:** Recharts 2.12.7
- **State Management:** React hooks (useState, useEffect)
- **Notifications:** Sonner toast library

### 3.2 Backend Integrations
- **IoT Platform:** ThingSpeak API for sensor data
- **Weather API:** Open-Meteo (free tier)
- **Geocoding:** OpenCage Geocoding API
- **AI/ML:** Google Gemini AI for recommendations and chat
- **SMS:** Twilio API for alerts
- **Voice AI:** ElevenLabs ConvAI, Omnidim API
- **IoT Control:** MQTT protocol for pump control

### 3.3 Data Sources
- **ThingSpeak Fields:**
  - Field 1: Soil Moisture (%)
  - Field 2: Temperature (°C)
  - Field 3: Humidity (%)
  - Field 5: Nitrogen (ppm)
  - Field 6: Phosphorus (ppm)
  - Field 7: Potassium (ppm)

### 3.4 Environment Variables Required
```
NEXT_PUBLIC_THINGSPEAK_CHANNEL_ID
NEXT_PUBLIC_THINGSPEAK_READ_API_KEY
NEXT_PUBLIC_OPENCAGE_API_KEY
NEXT_PUBLIC_GEMINI_API_KEY
TWILIO_ACCOUNT_SID
TWILIO_AUTH_TOKEN
TWILIO_PHONE_NUMBER
TWILIO_TO_PHONE_NUMBER
MQTT_BROKER_URL
MQTT_USERNAME
MQTT_PASSWORD
```

---

## 4. User Interface Requirements

### 4.1 Landing Page
- Hero section with value proposition
- Features showcase
- Benefits section
- Testimonials
- Pricing information
- FAQ section
- Contact form

### 4.2 Dashboard Layout
- Collapsible sidebar navigation
- Responsive mobile menu
- Logo and branding
- Navigation items:
  - Dashboard (overview)
  - Sensors (detailed analytics)
  - Alerts (notification center)
  - Recommendations (AI insights)
  - Balaram AI (chatbot)
  - My Farm (future)
  - Analytics (future)
  - Drone View (future)
  - Weather (detailed forecast)
  - Settings (future)
- Logout button

### 4.3 Color Scheme
- Primary: Green (#16A34A, #22C55E)
- Secondary: Blue (#2563EB, #3B82F6)
- Accent: Orange (#EA580C)
- Status Colors:
  - Success/Good: Green
  - Warning/Moderate: Yellow
  - Danger/Urgent: Red
- Neutral: Gray scale for text and backgrounds

### 4.4 Typography
- Primary Font: Inter (body text)
- Secondary Font: Poppins (headings)
- Font sizes: Responsive scale from 12px to 48px

---

## 5. Data Requirements

### 5.1 Data Collection
- Sensor data collected every 1-15 minutes (configurable)
- Weather data updated every 5 minutes
- AI recommendations generated on-demand and every 5 minutes
- Location data collected once per session or on-demand

### 5.2 Data Storage
- Client-side state management for current session
- Browser localStorage for user preferences (future)
- No persistent database in current version
- Historical data retrieved from ThingSpeak

### 5.3 Data Retention
- ThingSpeak: As per platform limits
- Recommendation history: Last 10 entries in memory
- Session data: Cleared on page refresh

---

## 6. Integration Requirements

### 6.1 ThingSpeak Integration
- Read sensor data via REST API
- Support for multiple time ranges
- Handle rate limits and errors
- Parse and validate all field data

### 6.2 Weather API Integration
- Fetch current weather by coordinates
- Detailed forecast data (future)
- Handle API timeouts (5-10 seconds)
- Cache responses appropriately

### 6.3 AI/ML Integration
- Send sensor data to recommendation endpoint
- Parse recommendation response
- Display confidence scores and reasoning
- Handle API failures gracefully

### 6.4 Communication Integrations
- Twilio SMS for critical alerts
- MQTT for real-time pump control
- Voice AI for hands-free interaction
- WebSocket for real-time updates (future)

---

## 7. Future Enhancements

### 7.1 Phase 2 Features
- User authentication and authorization
- Multi-farm support
- Crop-specific recommendations
- Yield prediction models
- Disease detection via image analysis
- Detailed weather forecasts
- Drone integration for aerial monitoring
- Advanced analytics and reporting

### 7.2 Phase 3 Features
- Mobile native apps (iOS/Android)
- Offline mode support
- Community features (farmer network)
- Marketplace integration
- Government scheme integration
- Multilingual support (Hindi, regional languages)
- Advanced automation rules
- Integration with agricultural equipment

---

## 8. Constraints and Assumptions

### 8.1 Constraints
- Free tier API limits for external services
- Browser geolocation accuracy limitations
- Network connectivity required for real-time features
- ThingSpeak data update frequency limits
- No user authentication in current version

### 8.2 Assumptions
- Users have modern smartphones or computers
- Internet connectivity is available (3G or better)
- Sensors are properly calibrated and installed
- ThingSpeak channel is properly configured
- Users understand basic farming concepts
- English language proficiency for current version

---

## 9. Success Metrics

### 9.1 User Engagement
- Daily active users
- Average session duration
- Feature usage frequency
- Recommendation acceptance rate

### 9.2 System Performance
- API response times
- Error rates
- Uptime percentage
- Data accuracy

### 9.3 Business Impact
- Crop yield improvements
- Water usage optimization
- Fertilizer cost reduction
- Early problem detection rate

---

## 10. Compliance and Standards

### 10.1 Data Privacy
- No personal data collection in current version
- Transparent data usage policies (future)
- GDPR compliance considerations (future)

### 10.2 Accessibility
- WCAG 2.1 Level AA target
- Keyboard navigation support
- Screen reader compatibility
- Color contrast ratios

### 10.3 Code Quality
- TypeScript strict mode
- ESLint configuration
- Component testing (future)
- Code documentation

---

## Document Version
- **Version:** 1.0
- **Last Updated:** February 2026
- **Status:** Active Development
