# 🌾 E-Bhoomi - Smart Agricultural IoT Dashboard

<div align="center">

![E-Bhoomi Logo](./public/logo.png)

**Empowering Farmers with AI-Driven Insights**

[![Next.js](https://img.shields.io/badge/Next.js-13.5.1-black?style=for-the-badge&logo=next.js)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-18.2.0-blue?style=for-the-badge&logo=react)](https://reactjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.2.2-blue?style=for-the-badge&logo=typescript)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-3.3.3-38B2AC?style=for-the-badge&logo=tailwind-css)](https://tailwindcss.com/)

[Features](#-features) • [Demo](#-demo) • [Installation](#-installation) • [Usage](#-usage) • [Competition](#-ai-4-bharat-competition) • [Contributing](#-contributing)

</div>

---

## 📖 About The Project

**E-Bhoomi** is an intelligent agricultural monitoring and management platform developed for the **AI 4 Bharat** competition. The platform leverages IoT sensors, real-time data analytics, and AI-powered recommendations to help farmers optimize crop health, resource usage, and yield.

### 🏆 AI 4 Bharat Competition

This project was created as part of the **AI for Bharat** initiative, a comprehensive AI developer program designed to help developers build functional, deployable AI solutions that address India's most pressing challenges. The competition focuses on:

- 🌾 **Agriculture Innovation:** Building AI solutions to improve farming practices and crop yields
- 🏥 **Healthcare Access:** Enhancing medical services in rural areas
- 📚 **Education Equity:** Making quality education accessible to all
- 🌍 **Sustainable Development:** Creating solutions for India's unique challenges

The AI for Bharat program provides hands-on workshops, expert mentorship, and a national competition platform where participants gain practical experience with enterprise-grade AI tools while building solutions that matter for India. Through this initiative, developers move from AI concepts to building functional, deployable solutions that can make a real impact on millions of lives.

**E-Bhoomi** specifically addresses the agricultural challenges faced by Indian farmers by:
- Providing real-time monitoring of soil conditions and weather
- Offering AI-powered recommendations for fertilizer, pesticide, and irrigation
- Enabling smart automation for water management
- Making advanced agricultural technology accessible and affordable

---

## ✨ Features

### 🔍 Real-Time Monitoring
- **Live Sensor Data:** Monitor soil moisture, temperature, humidity, and NPK levels in real-time
- **Historical Trends:** Visualize data patterns over multiple time ranges (1 hour to 1 year)
- **Interactive Charts:** Zoom, pan, and analyze trends with responsive visualizations
- **Weather Integration:** Get current weather conditions based on your location

### 🤖 AI-Powered Intelligence
- **Smart Recommendations:** Receive AI-driven suggestions for:
  - 🌱 Fertilizer application
  - 🛡️ Pesticide management
  - 💧 Irrigation scheduling
  - 👁️ Crop monitoring
- **Confidence Scoring:** Each recommendation includes a confidence level
- **Contextual Analysis:** Understand the reasoning behind each suggestion
- **Balaram AI Chatbot:** Ask questions and get intelligent responses about your farm

### 💧 Smart Irrigation Control
- **Manual Mode:** Control your water pump with a single click
- **Automatic Mode:** Let AI manage irrigation based on soil moisture
  - Auto-ON when moisture ≤ 10%
  - Auto-OFF when moisture ≥ 80%
- **Safety Features:** Automatic shutoff to prevent overwatering
- **Real-Time Status:** Visual indicators show pump state

### 🚨 Alert System
- **SMS Notifications:** Receive critical alerts via Twilio
- **In-App Alerts:** Toast notifications for all important events
- **Threshold Monitoring:** Automatic alerts for extreme conditions
- **Alert History:** Track all notifications and actions taken

### 📱 Responsive Design
- **Mobile-First:** Optimized for smartphones and tablets
- **Desktop Dashboard:** Full-featured experience on larger screens
- **Collapsible Sidebar:** Maximize screen space when needed
- **Touch-Friendly:** Large tap targets for easy mobile interaction

### 🎙️ Voice & Camera AI
- **Voice Commands:** Interact hands-free with voice input
- **Camera Integration:** Visual crop analysis (future enhancement)
- **Speech-to-Text:** Natural language queries
- **AI Voice Calls:** Schedule consultations via Omnidim integration

---

## 🛠️ Technology Stack

### Frontend
- **Framework:** Next.js 13.5.1 (React 18.2.0)
- **Language:** TypeScript 5.2.2
- **Styling:** Tailwind CSS 3.3.3
- **UI Components:** Radix UI
- **Charts:** Recharts 2.12.7
- **Notifications:** Sonner

### Backend & APIs
- **IoT Platform:** ThingSpeak API
- **Weather:** Open-Meteo API
- **Geocoding:** OpenCage API
- **AI/ML:** Google Gemini AI
- **SMS:** Twilio API
- **Voice AI:** ElevenLabs ConvAI, Omnidim
- **IoT Control:** MQTT Protocol

### Key Libraries
```json
{
  "@google/generative-ai": "^0.21.0",
  "@radix-ui/react-*": "Latest",
  "recharts": "^2.12.7",
  "mqtt": "^5.14.1",
  "sonner": "^1.7.4",
  "lucide-react": "^0.446.0"
}
```

---

## 🚀 Installation

### Prerequisites
- Node.js 18+ and npm/yarn
- ThingSpeak account with configured channel
- API keys for external services (see Environment Variables)

### Step 1: Clone the Repository
```bash
git clone https://github.com/yourusername/e-bhoomi.git
cd e-bhoomi
```

### Step 2: Install Dependencies
```bash
npm install
# or
yarn install
```

### Step 3: Configure Environment Variables
Create a `.env.local` file in the root directory:

```env
# ThingSpeak Configuration
NEXT_PUBLIC_THINGSPEAK_CHANNEL_ID=your_channel_id
NEXT_PUBLIC_THINGSPEAK_READ_API_KEY=your_read_api_key

# Weather API
NEXT_PUBLIC_OPENCAGE_API_KEY=your_opencage_api_key

# AI Configuration
NEXT_PUBLIC_GEMINI_API_KEY=your_gemini_api_key

# Twilio SMS (Optional)
TWILIO_ACCOUNT_SID=your_account_sid
TWILIO_AUTH_TOKEN=your_auth_token
TWILIO_PHONE_NUMBER=your_twilio_number
TWILIO_TO_PHONE_NUMBER=recipient_number

# MQTT Configuration (Optional)
MQTT_BROKER_URL=mqtt://your_broker_url
MQTT_USERNAME=your_username
MQTT_PASSWORD=your_password
```

### Step 4: Run Development Server
```bash
npm run dev
# or
yarn dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

### Step 5: Build for Production
```bash
npm run build
npm run start
# or
yarn build
yarn start
```

---

## 📊 ThingSpeak Setup

### Channel Configuration
Configure your ThingSpeak channel with the following fields:

| Field | Parameter | Unit |
|-------|-----------|------|
| Field 1 | Soil Moisture | % |
| Field 2 | Temperature | °C |
| Field 3 | Humidity | % |
| Field 5 | Nitrogen (N) | ppm |
| Field 6 | Phosphorus (P) | ppm |
| Field 7 | Potassium (K) | ppm |

### Sensor Integration
1. Create a ThingSpeak channel
2. Note your Channel ID and Read API Key
3. Configure your IoT devices to send data to ThingSpeak
4. Update environment variables with your credentials

---

## 🎮 Usage

### Dashboard Navigation
1. **Dashboard:** Overview of all metrics and quick actions
2. **Sensors:** Detailed analytics with historical charts
3. **Alerts:** Notification center for all alerts
4. **Recommendations:** AI-powered farming suggestions
5. **Balaram AI:** Interactive chatbot for queries
6. **My Farm:** Farm management (coming soon)
7. **Analytics:** Advanced data analysis (coming soon)
8. **Weather:** Detailed weather forecasts (coming soon)

### Pump Control
1. Navigate to **Sensors** page
2. Toggle between **Manual** and **Automatic** mode
3. In Manual mode, click the pump button to control
4. In Automatic mode, the system manages irrigation based on soil moisture

### Getting Recommendations
1. Ensure sensors are sending data to ThingSpeak
2. Navigate to **Recommendations** page
3. Click **Refresh** to get latest AI analysis
4. Review the suggested action and confidence score
5. View recommendation history for tracking

### Using Balaram AI
1. Navigate to **Balaram AI** page
2. Choose between **Voice/Camera AI** or **Chatbot** tab
3. Type or speak your question
4. Receive intelligent responses based on your farm data

---

## 🏗️ Project Structure

```
e-bhoomi/
├── app/
│   ├── page.tsx                 # Landing page
│   ├── layout.tsx               # Root layout
│   ├── dashboard/
│   │   ├── page.tsx            # Main dashboard
│   │   ├── layout.tsx          # Dashboard layout
│   │   ├── sensors/            # Sensor analytics
│   │   ├── alerts/             # Alert center
│   │   ├── recommendations/    # AI recommendations
│   │   └── balaram-ai/         # AI chatbot
│   └── api/
│       ├── alerts/             # Alert API routes
│       ├── pump/               # Pump control API
│       └── ml/                 # ML recommendation API
├── lib/
│   ├── thingspeak.ts           # ThingSpeak service
│   ├── weather.ts              # Weather service
│   ├── agricultureService.ts  # AI recommendation service
│   ├── twilio.ts               # SMS service
│   └── utils.ts                # Utility functions
├── components/
│   ├── ui/                     # Reusable UI components
│   ├── navigation/             # Navigation components
│   └── sections/               # Landing page sections
├── public/                     # Static assets
├── docs/                       # Documentation
└── README.md                   # This file
```

---

## 🔧 Configuration

### Time Range Settings
Adjust polling intervals in `lib/thingspeak.ts`:
```typescript
const POLLING_INTERVALS = {
  '1h': 15000,    // 15 seconds
  '24h': 60000,   // 1 minute
  '7d': 300000,   // 5 minutes
  '30d': 900000,  // 15 minutes
  '1y': 3600000   // 1 hour
};
```

### Alert Thresholds
Modify alert conditions in `app/dashboard/page.tsx`:
```typescript
if (currentData.soilMoisture > 80 || currentData.soilMoisture < 20) {
  // Send alert
}
```

### Pump Automation
Adjust automatic pump thresholds in `app/dashboard/sensors/page.tsx`:
```typescript
if (latestSoilMoisture <= 10) setPump('on');
if (latestSoilMoisture >= 80) setPump('off');
```

---

## 🧪 Testing

### Manual Testing
```bash
# Run development server
npm run dev

# Test checklist:
# - Dashboard loads with sensor data
# - Charts render for all time ranges
# - Location detection works
# - Pump control functions
# - Alerts trigger correctly
# - AI recommendations display
# - Chatbot responds to queries
```

### Future: Automated Testing
```bash
# Unit tests (planned)
npm run test

# E2E tests (planned)
npm run test:e2e
```

---

## 🚀 Deployment

### Vercel (Recommended)
1. Push code to GitHub
2. Import project in Vercel
3. Configure environment variables
4. Deploy automatically

### Manual Deployment
```bash
# Build production bundle
npm run build

# Start production server
npm run start
```

### Docker (Future)
```bash
# Build image
docker build -t e-bhoomi .

# Run container
docker run -p 3000:3000 e-bhoomi
```

---
## 🤝 Contributing

We welcome contributions from the community! Here's how you can help:

### Reporting Bugs
1. Check if the bug is already reported
2. Create a detailed issue with:
   - Steps to reproduce
   - Expected behavior
   - Actual behavior
   - Screenshots (if applicable)

### Suggesting Features
1. Open an issue with the `enhancement` label
2. Describe the feature and its benefits
3. Provide use cases and examples

### Pull Requests
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Code Style
- Follow TypeScript best practices
- Use Prettier for formatting
- Write meaningful commit messages
- Add comments for complex logic

---

## 👥 Team

**E-Bhoomi Development Team**
- Project Lead: Rup Roshan Jha
- AI/ML Engineer: Ridhibrata Das
- Full Stack Developer: Soumyadip Pal
- IoT Specialist: Ankit Mondal
---

## 🙏 Acknowledgments

- **AI for Bharat Initiative** for organizing the competition and providing a platform to build impactful solutions
- **ThingSpeak** for IoT data platform
- **Open-Meteo** for free weather API
- **Google Gemini AI** for AI capabilities
- **Vercel** for hosting platform
- **Open Source Community** for amazing libraries and tools


---

## 📚 Documentation

- [Requirements Document](./requirements.md)
- [Design Document](./design.md)

---

## 💡 Inspiration

> "Agriculture is our wisest pursuit, because it will in the end contribute most to real wealth, good morals, and happiness." - Thomas Jefferson

E-Bhoomi was born from the vision of making advanced agricultural technology accessible to every farmer in India. By combining IoT sensors, AI intelligence, and user-friendly interfaces, we aim to empower farmers with the tools they need to increase yields, reduce costs, and build sustainable farming practices.

---

<div align="center">

**Made with ❤️ for Indian Farmers**

**AI 4 Bharat Competition 2026**

[⬆ Back to Top](#-e-bhoomi---smart-agricultural-iot-dashboard)

</div>

