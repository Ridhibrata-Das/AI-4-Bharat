# Design Document: E-Bhoomi Agricultural IoT Dashboard

## Overview

E-Bhoomi is a Next.js 13-based agricultural IoT dashboard that provides comprehensive farm monitoring and management capabilities. The system architecture follows a modern web application pattern with server-side rendering, API routes for backend logic, and integration with multiple external services.

### Key Design Principles

1. **Separation of Concerns**: Clear separation between UI components, business logic, and external service integrations
2. **Real-time Data**: Polling-based updates with configurable intervals based on data freshness requirements
3. **Resilience**: Graceful degradation when external services are unavailable
4. **Performance**: Caching strategies to minimize API calls and optimize load times
5. **Extensibility**: Modular architecture allowing easy addition of new sensors and features

### Technology Stack

- **Frontend Framework**: Next.js 13 with App Router
- **Language**: TypeScript for type safety
- **UI Library**: React 18 with Radix UI components
- **Styling**: Tailwind CSS for responsive design
- **Charts**: Recharts for data visualization
- **State Management**: React hooks (useState, useEffect)
- **API Communication**: Fetch API with Next.js route handlers
- **IoT Protocol**: MQTT over TLS for pump control
- **AI Integration**: Google Gemini for multimodal AI capabilities

## Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Client Browser                           │
│  ┌──────────────────────────────────────────────────────┐  │
│  │         Next.js Frontend (React Components)          │  │
│  │  - Dashboard Pages                                    │  │
│  │  - Real-time Charts                                   │  │
│  │  - Interactive Controls                               │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ HTTP/HTTPS
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Next.js Server (API Routes)                     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  /api/alerts          - SMS alert triggering         │  │
│  │  /api/pump            - MQTT pump control            │  │
│  │  /api/ml/agriculture  - ML recommendation proxy      │  │
│  │  /api/omnidim/call    - AI voice call initiation     │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼
┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│  ThingSpeak  │   │    Twilio    │   │  OpenMeteo   │
│  IoT Data    │   │  SMS Alerts  │   │   Weather    │
└──────────────┘   └──────────────┘   └──────────────┘
        │                   │                   │
        ▼                   ▼                   ▼
┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│ Google Gemini│   │   OpenCage   │   │   Omnidim    │
│  AI Chatbot  │   │  Geocoding   │   │  AI Calls    │
└──────────────┘   └──────────────┘   └──────────────┘
        │
        ▼
┌──────────────┐
│ ML Service   │
│ (External)   │
└──────────────┘
        │
        ▼
┌──────────────┐
│ MQTT Broker  │
│ Pump Control │
└──────────────┘
```

### Data Flow Architecture

1. **Sensor Data Flow**:
   - IoT sensors → ThingSpeak → Next.js API → Frontend components
   - Polling intervals: 15s to 1h based on time range selection
   - Caching: 30-second revalidation for sensor data

2. **Control Flow**:
   - User action → Frontend → API route → MQTT broker → Physical pump
   - Automatic mode: Sensor reading → Logic evaluation → MQTT command

3. **Alert Flow**:
   - Sensor threshold breach → Alert API → Twilio → SMS to farmer
   - Parallel: Toast notification to dashboard user

4. **AI Recommendation Flow**:
   - Sensor data → ML Service API → Recommendation engine → Dashboard display
   - Refresh: Every 5 minutes or manual trigger

## Components and Interfaces

### Frontend Components

#### 1. Dashboard Page (`dashboard/page.tsx`)

**Purpose**: Main overview page displaying key metrics, charts, and quick actions

**Key Features**:
- Real-time sensor cards (temperature, humidity, soil moisture, NPK)
- Historical data charts with time range selection
- AI recommendation display
- Location detection and weather integration
- Quick action buttons

**State Management**:
```typescript
interface DashboardState {
  weatherData: WeatherData;
  soilMoistureData: SoilMoistureData;
  npkData: NPKData;
  location: LocationData;
  historyData: ThingSpeakData[];
  npkHistoryData: NPKData[];
  selectedRange: TimeRange;
  isLoading: boolean;
  soilMoistureTrend: TrendData;
  npkTrend: TrendData;
  agricultureRecommendation: AgricultureRecommendation | null;
}
```

**Polling Strategy**:
- Interval based on selected time range
- Cleanup on component unmount
- Automatic refresh on location change

#### 2. Sensors Page (`dashboard/sensors/page.tsx`)

**Purpose**: Detailed sensor monitoring with pump control interface

**Key Features**:
- Individual charts for each sensor type
- Manual/Automatic pump control toggle
- Real-time pump state display
- Auto-cutoff safety mechanism

**Pump Control Logic**:
```typescript
// Automatic mode
if (modeAutomatic && soilMoisture <= 10 && pumpState !== 'on') {
  setPump('on');
}
if (modeAutomatic && soilMoisture >= 80 && pumpState !== 'off') {
  setPump('off');
}

// Manual mode auto-cutoff
if (!modeAutomatic && pumpState === 'on' && soilMoisture >= 80) {
  setPump('off');
}
```

#### 3. Alerts Page (`dashboard/alerts/page.tsx`)

**Purpose**: Alert configuration and management interface

**Key Features**:
- Alert type configuration (Temperature High/Low, Humidity High/Low, Soil Moisture Low)
- Threshold value adjustment
- Enable/disable toggles
- Test alert functionality

**State Structure**:
```typescript
interface Alert {
  id: number;
  type: string;
  threshold: number;
  enabled: boolean;
}
```

#### 4. Recommendations Page (`dashboard/recommendations/page.tsx`)

**Purpose**: AI-powered agricultural recommendations with history

**Key Features**:
- Current recommendation display with urgency indicators
- Recommendation history (last 10)
- Dataset statistics
- Manual refresh capability

**Recommendation Display**:
- Action type with icon and color coding
- Confidence score percentage
- Semantic tag description
- NDI/PDI labels
- Sensor data snapshot

#### 5. Balaram AI Page (`dashboard/balaram-ai/page.tsx`)

**Purpose**: Multimodal AI assistant with voice, camera, and text capabilities

**Key Features**:
- Tab-based interface (Voice/Camera AI, Chatbot)
- Camera preview component
- Voice recognition integration
- Message history display
- Google Gemini integration

**Component Structure**:
```typescript
interface Message {
  type: 'human' | 'gemini';
  text: string;
  timestamp: string;
}
```

#### 6. Weather Page (`dashboard/weather/page.tsx`)

**Purpose**: Comprehensive weather information and agricultural forecasts

**Key Features**:
- Current weather conditions
- 5-day forecast
- Detailed metrics (pressure, visibility, UV index, feels-like temperature)
- Agricultural recommendations based on weather
- Location detection

**Weather Data Structure**:
```typescript
interface DetailedWeatherData {
  current: {
    temperature: number;
    condition: string;
    humidity: number;
    windSpeed: number;
    pressure: number;
    visibility: number;
    uvIndex: number;
    feelsLike: number;
  };
  forecast: Array<{
    day: string;
    high: number;
    low: number;
    condition: string;
    icon: IconComponent;
    precipitation: number;
  }>;
}
```

#### 7. Analytics Page (`dashboard/analytics/page.tsx`)

**Purpose**: Performance tracking and insights

**Key Features**:
- Yield tracking with trends
- Resource usage monitoring (water, fertilizer, energy)
- AI-generated insights
- Export functionality
- Filter options

### Service Layer

#### 1. ThingSpeak Service (`lib/thingspeak.ts`)

**Purpose**: Interface with ThingSpeak IoT platform for sensor data

**Key Functions**:

```typescript
// Fetch soil moisture with history and trend
async function fetchSoilMoistureData(range: string): Promise<{
  currentData: SoilMoistureData;
  historyData: ThingSpeakData[];
  trend: TrendData;
}>

// Fetch historical sensor data
async function fetchThingSpeakHistory(timeRange: string): Promise<ThingSpeakData[]>

// Fetch NPK sensor data
async function fetchNPKData(timeRange: string): Promise<NPKData[]>

// Fetch current NPK with trend
async function fetchCurrentNPK(): Promise<{
  currentData: NPKData;
  trend: TrendData;
}>

// Fetch latest sensor reading
async function fetchLatestSensorData(): Promise<ThingSpeakFeed | null>
```

**Data Parsing**:
- Handles missing/invalid values with fallback to zero
- Converts string fields to numbers
- Calculates trends from current vs previous readings
- Formats timestamps for display

**Result Count Mapping**:
```typescript
const resultCounts = {
  '1h': 60,     // 1 minute intervals
  '24h': 144,   // 10 minute intervals  
  '7d': 168,    // 1 hour intervals
  '30d': 720,   // 1 hour intervals
  '1y': 8760    // 1 hour intervals
};
```

#### 2. Weather Service (`lib/weather.ts`)

**Purpose**: Interface with OpenMeteo API for weather data

**Key Functions**:

```typescript
// Fetch basic weather data
async function fetchWeatherData(
  latitude: number, 
  longitude: number
): Promise<WeatherData>

// Fetch detailed weather with forecast
async function fetchDetailedWeatherData(
  latitude: number, 
  longitude: number
): Promise<DetailedWeatherData>
```

**Weather Code Mapping**:
- Maps numeric weather codes to human-readable conditions
- Provides icon suggestions for UI display
- Handles various weather phenomena (clear, cloudy, rain, snow, thunderstorm, etc.)

**Feels-Like Calculation**:
```typescript
const feelsLike = temperature + (humidity / 100) * 2 - (windSpeed / 10);
```

#### 3. Agriculture Service (`lib/agricultureService.ts`)

**Purpose**: Interface with ML service for agricultural recommendations

**Key Functions**:

```typescript
// Get recommendation based on current sensor data
async function getAgricultureRecommendation(): Promise<AgricultureRecommendation | null>

// Fetch dataset statistics
async function getAgricultureStatistics(): Promise<Statistics>

// Get NPK range information
async function getNPKRanges(): Promise<NPKRanges>

// Helper functions for UI display
function getActionColor(action: string): string
function getActionUrgency(action: string): 'urgent' | 'moderate' | 'good'
function getUrgencyColor(urgency: string): string
function getActionIcon(action: string): string
```

**Recommendation Structure**:
```typescript
interface AgricultureRecommendation {
  action: string;              // Apply Fertilizer, Apply Pesticide, Irrigate, Monitor
  semantic_tag: string;        // Human-readable description
  ndi_label: string;          // Nitrogen Deficiency Index
  pdi_label: string;          // Phosphorus Deficiency Index
  confidence: number;         // 0-1 confidence score
  score: number;              // Match score
  matched_record?: any;       // Optional matched dataset record
}
```

#### 4. Twilio Service (`lib/twilio.ts`)

**Purpose**: Send SMS alerts via Twilio API

**Implementation**:

```typescript
async function sendSMSAlert(message: string): Promise<TwilioResponse> {
  const url = `https://api.twilio.com/2010-04-01/Accounts/${accountSid}/Messages.json`;
  const auth = Buffer.from(`${accountSid}:${authToken}`).toString('base64');
  
  return fetch(url, {
    method: 'POST',
    headers: {
      'Authorization': `Basic ${auth}`,
      'Content-Type': 'application/x-www-form-urlencoded'
    },
    body: new URLSearchParams({
      'To': toPhone,
      'From': twilioPhone,
      'Body': message
    })
  });
}
```

**Error Handling**:
- Logs errors to console
- Throws exceptions for upstream handling
- Returns Twilio message SID on success

#### 5. Omnidim Service (`lib/omnidim.ts`)

**Purpose**: Trigger AI voice calls via Omnidim platform

**Implementation**:

```typescript
async function triggerOmnidimAICall(
  payload?: Record<string, unknown>
): Promise<OmnidimCallResponse> {
  const url = `${baseUrl}/v1/agents/${agentId}/calls`;
  
  return fetch(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${apiKey}`
    },
    body: JSON.stringify(payload)
  });
}
```

### API Routes

#### 1. Alerts API (`app/api/alerts/route.ts`)

**Endpoint**: `POST /api/alerts`

**Purpose**: Trigger SMS alerts based on soil moisture thresholds

**Request Body**:
```typescript
{
  soilMoisture: number;
}
```

**Logic**:
```typescript
if (soilMoisture > 80) {
  await sendSMSAlert("Plant is drowning, please remove water");
} else if (soilMoisture < 20) {
  await sendSMSAlert("Please water the plants");
}
```

**Response**:
```typescript
{ success: true } | { error: string }
```

#### 2. Pump Control API (`app/api/pump/route.ts`)

**Endpoint**: `POST /api/pump`

**Purpose**: Control irrigation pump via MQTT

**Request Body**:
```typescript
{
  action?: 'ON' | 'OFF' | 'on' | 'off';
  state?: 'ON' | 'OFF' | 'on' | 'off';
  value?: 1 | 0;
}
```

**MQTT Configuration**:
- Protocol: mqtts (MQTT over TLS)
- Port: 8883
- QoS: 1 (at least once delivery)
- Retain: false

**Connection Management**:
- Singleton MQTT client instance
- Automatic reconnection with 1-second interval
- Connection validation before publishing

**Response**:
```typescript
{
  success: true;
  sent: 'ON' | 'OFF';
  topic: string;
} | { error: string }
```

#### 3. ML Agriculture API (`app/api/ml/agriculture/recommendation/route.ts`)

**Endpoints**: 
- `POST /api/ml/agriculture/recommendation` - Get recommendation
- `GET /api/ml/agriculture/recommendation?type=statistics` - Get statistics
- `GET /api/ml/agriculture/recommendation?type=npk-ranges` - Get NPK ranges

**POST Request Body**:
```typescript
{
  n: number;              // Nitrogen level
  p: number;              // Phosphorus level
  k: number;              // Potassium level
  temperature: number;    // Temperature in Celsius
  humidity: number;       // Humidity percentage
  soil_moisture?: number; // Optional soil moisture
}
```

**Validation**:
- Ensures all required numeric fields are present
- Returns 400 error for missing/invalid data

**Proxy Pattern**:
- Forwards requests to external ML service
- Handles ML service errors gracefully
- Returns ML service responses directly

#### 4. Omnidim Call API (`app/api/omnidim/call/route.ts`)

**Endpoint**: `POST /api/omnidim/call`

**Purpose**: Initiate AI voice calls via Omnidim

**Request Body**:
```typescript
{
  to: string;              // E.164 format phone number
  phoneNumber?: string;    // Alternative field name
  call_context?: {         // Optional context
    source?: string;
    intent?: string;
    customer_name?: string;
    account_id?: string;
    priority?: string;
  };
}
```

**Omnidim API Call**:
```typescript
POST /api/v1/calls/dispatch
{
  agent_id: number | string;
  to_number: string;
  from_number_id?: string;
  call_context?: object;
}
```

**Response**:
```typescript
{
  success: true;
  data: OmnidimResponse;
} | { error: string }
```

## Data Models

### Sensor Data Models

```typescript
// Basic sensor reading
interface ThingSpeakData {
  soilMoisture: number;
  temperature: number;
  humidity: number;
  time: string;
}

// NPK sensor reading
interface NPKData {
  nitrogen: number;
  phosphorus: number;
  potassium: number;
  time: string;
}

// Soil moisture specific
interface SoilMoistureData {
  soilMoisture: number;
  time: string;
}

// Trend information
interface TrendData {
  change: number;      // Percentage change
  increasing: boolean; // Direction of change
}

// ThingSpeak API response
interface ThingSpeakResponse {
  feeds: Array<{
    created_at: string;
    field1: string; // soil moisture
    field2: string; // temperature
    field3: string; // humidity
    field5: string; // nitrogen (N)
    field6: string; // phosphorus (P)
    field7: string; // potassium (K)
  }>;
}
```

### Weather Data Models

```typescript
// Basic weather data
interface WeatherData {
  temperature: number;
  humidity: number;
  time: string;
}

// Detailed weather with forecast
interface DetailedWeatherData {
  current: {
    temperature: number;
    condition: string;
    humidity: number;
    windSpeed: number;
    pressure: number;
    visibility: number;
    uvIndex: number;
    feelsLike: number;
  };
  forecast: Array<{
    day: string;
    high: number;
    low: number;
    condition: string;
    icon: any;
    precipitation: number;
  }>;
}
```

### Location Data Models

```typescript
interface LocationData {
  latitude: number;
  longitude: number;
  locationName: string;
}

interface GeolocationPosition {
  coords: {
    latitude: number;
    longitude: number;
    accuracy: number;
  };
  timestamp: number;
}
```

### Recommendation Data Models

```typescript
interface AgricultureRecommendation {
  action: string;
  semantic_tag: string;
  ndi_label: string;
  pdi_label: string;
  confidence: number;
  score: number;
  matched_record?: any;
}

interface RecommendationHistory {
  id: string;
  recommendation: AgricultureRecommendation;
  timestamp: string;
  sensorData: {
    n: number;
    p: number;
    k: number;
    temperature: number;
    humidity: number;
    soil_moisture: number;
  };
}
```

### Alert Data Models

```typescript
interface Alert {
  id: number;
  type: string;
  threshold: number;
  enabled: boolean;
}

type AlertType = 
  | 'Temperature High'
  | 'Temperature Low'
  | 'Humidity High'
  | 'Humidity Low'
  | 'Soil Moisture Low';
```

### UI State Models

```typescript
// Time range selection
type TimeRange = '1h' | '24h' | '7d' | '30d' | '1y';

// Pump state
type PumpState = 'on' | 'off' | 'unknown';

// Pump mode
type PumpMode = 'manual' | 'automatic';

// Urgency level
type UrgencyLevel = 'urgent' | 'moderate' | 'good';

// Message type for AI chat
interface Message {
  type: 'human' | 'gemini';
  text: string;
}
```

## Data Models (continued)

### Configuration Models

```typescript
// Environment configuration
interface EnvironmentConfig {
  // ThingSpeak
  NEXT_PUBLIC_THINGSPEAK_CHANNEL_ID: string;
  NEXT_PUBLIC_THINGSPEAK_READ_API_KEY: string;
  
  // Twilio
  TWILIO_ACCOUNT_SID: string;
  TWILIO_AUTH_TOKEN: string;
  TWILIO_TO_PHONE: string;
  
  // MQTT
  MQTT_URL: string;
  MQTT_PORT: number;
  MQTT_USERNAME: string;
  MQTT_PASSWORD: string;
  MQTT_TOPIC: string;
  
  // Google Gemini
  NEXT_PUBLIC_GEMINI_API_KEY: string;
  
  // OpenCage
  NEXT_PUBLIC_OPENCAGE_API_KEY: string;
  
  // Omnidim
  NEXT_PUBLIC_OMNIDIM_BASE_URL: string;
  NEXT_PUBLIC_OMNIDIM_API_KEY: string;
  NEXT_PUBLIC_OMNIDIM_AGENT_ID: string;
  NEXT_PUBLIC_OMNIDIM_FROM_NUMBER_ID: string;
  
  // ML Service
  ML_SERVICE_URL: string;
}

// MQTT client configuration
interface MQTTConfig {
  host: string;
  port: number;
  protocol: 'mqtts';
  username: string;
  password: string;
  reconnectPeriod: number;
}
```

## Error Handling

### Error Handling Strategy

1. **API Call Errors**:
   - Catch and log all errors
   - Display user-friendly toast notifications
   - Provide retry mechanisms where appropriate
   - Graceful degradation (show last known data)

2. **Timeout Handling**:
   - ThingSpeak: 10-second timeout with AbortController
   - Weather API: 5-second timeout
   - Display timeout-specific error messages

3. **Validation Errors**:
   - Validate inputs before API calls
   - Return 400 status codes with descriptive messages
   - Display inline validation errors in forms

4. **Configuration Errors**:
   - Check for missing environment variables
   - Display clear error messages indicating which variables are needed
   - Prevent application startup with critical missing config

### Error Response Patterns

```typescript
// API route error response
{
  error: string;           // User-friendly error message
  details?: string;        // Optional technical details
  status?: number;         // HTTP status code
}

// Service layer error handling
try {
  const result = await externalAPI();
  return result;
} catch (error) {
  console.error('Detailed error:', error);
  if (error instanceof Error) {
    if (error.name === 'AbortError') {
      throw new Error('Request timed out');
    }
    throw error;
  }
  throw new Error('Unknown error occurred');
}
```

### User Feedback Patterns

```typescript
// Success notification
toast.success('Operation completed successfully');

// Error notification
toast.error('Failed to fetch data. Please try again.');

// Warning notification
toast.warning('Data may be outdated');

// Info notification
toast.info('Alert: Soil moisture is low');
```

## Testing Strategy

### Testing Approach

The E-Bhoomi system requires comprehensive testing across multiple layers:

1. **Unit Tests**: Test individual functions and components in isolation
2. **Integration Tests**: Test interactions between components and services
3. **End-to-End Tests**: Test complete user workflows
4. **Property-Based Tests**: Verify universal properties across all inputs

### Unit Testing Focus Areas

1. **Data Parsing and Validation**:
   - Test `parseNumericValue` with various inputs (valid numbers, empty strings, null, undefined)
   - Test coordinate validation logic
   - Test phone number format validation
   - Test threshold value validation

2. **Calculation Functions**:
   - Test trend calculation with various current/previous values
   - Test feels-like temperature calculation
   - Test NPK average calculation
   - Test distance calculation between coordinates

3. **Helper Functions**:
   - Test `getActionColor` with all action types
   - Test `getActionUrgency` with all action types
   - Test `getMoistureStatus` with boundary values
   - Test weather code mapping

4. **Component Rendering**:
   - Test SensorCard renders correctly with valid data
   - Test chart components render with empty data
   - Test loading states display correctly
   - Test error states display correctly

### Integration Testing Focus Areas

1. **API Route Integration**:
   - Test alerts API triggers SMS correctly
   - Test pump API publishes MQTT messages
   - Test ML API proxies requests correctly
   - Test Omnidim API formats requests correctly

2. **Service Integration**:
   - Test ThingSpeak service fetches and parses data
   - Test weather service handles API responses
   - Test agriculture service combines sensor data and ML responses
   - Test Twilio service sends SMS

3. **Component Integration**:
   - Test dashboard page fetches and displays all data
   - Test sensors page controls pump correctly
   - Test recommendations page displays history
   - Test weather page updates on location change

### End-to-End Testing Scenarios

1. **Monitoring Workflow**:
   - User opens dashboard
   - System fetches sensor data
   - Charts display historical data
   - User changes time range
   - Charts update with new data

2. **Pump Control Workflow**:
   - User navigates to sensors page
   - User toggles to automatic mode
   - Soil moisture drops below 10%
   - System turns pump ON
   - Soil moisture reaches 80%
   - System turns pump OFF

3. **Alert Workflow**:
   - Soil moisture exceeds 80%
   - System triggers alert API
   - SMS is sent via Twilio
   - Toast notification appears
   - User receives SMS

4. **Recommendation Workflow**:
   - User navigates to recommendations page
   - System fetches latest sensor data
   - System calls ML service
   - Recommendation displays with urgency
   - User clicks refresh
   - New recommendation appears

### Property-Based Testing

Property-based testing will be implemented using a suitable library for TypeScript (e.g., fast-check). Each test should run a minimum of 100 iterations.

