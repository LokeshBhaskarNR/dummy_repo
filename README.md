# Digital Prescription Application - Complete Setup Guide

## Project Structure

```
digital-prescription-app/
├── backend/
│   ├── src/
│   │   ├── config/
│   │   │   ├── database.js
│   │   │   ├── passport.js
│   │   │   ├── twilio.js
│   │   │   └── stripe.js
│   │   ├── models/
│   │   │   ├── User.js
│   │   │   ├── Doctor.js
│   │   │   ├── Laboratory.js
│   │   │   ├── Admin.js
│   │   │   ├── Patient.js
│   │   │   ├── Prescription.js
│   │   │   ├── RareCase.js
│   │   │   ├── Note.js
│   │   │   ├── LabReport.js
│   │   │   ├── Collaboration.js
│   │   │   └── Subscription.js
│   │   ├── controllers/
│   │   │   ├── authController.js
│   │   │   ├── doctorController.js
│   │   │   ├── labController.js
│   │   │   ├── adminController.js
│   │   │   ├── prescriptionController.js
│   │   │   ├── patientController.js
│   │   │   └── paymentController.js
│   │   ├── routes/
│   │   │   ├── auth.js
│   │   │   ├── doctor.js
│   │   │   ├── lab.js
│   │   │   ├── admin.js
│   │   │   ├── prescription.js
│   │   │   └── payment.js
│   │   ├── middleware/
│   │   │   ├── auth.js
│   │   │   ├── roleCheck.js
│   │   │   └── uploadHandler.js
│   │   ├── utils/
│   │   │   ├── pdfGenerator.js
│   │   │   ├── imageCompressor.js
│   │   │   ├── twilioService.js
│   │   │   └── emailService.js
│   │   └── server.js
│   ├── uploads/
│   ├── .env
│   ├── package.json
│   └── README.md
├── frontend/
│   ├── public/
│   │   └── index.html
│   ├── src/
│   │   ├── components/
│   │   │   ├── Auth/
│   │   │   │   ├── Login.jsx
│   │   │   │   └── ProtectedRoute.jsx
│   │   │   ├── Doctor/
│   │   │   │   ├── Dashboard.jsx
│   │   │   │   ├── PrescriptionCanvas.jsx
│   │   │   │   ├── DrawingToolbar.jsx
│   │   │   │   ├── PrescriptionHistory.jsx
│   │   │   │   ├── RareCases.jsx
│   │   │   │   ├── Notes.jsx
│   │   │   │   ├── PatientView.jsx
│   │   │   │   └── ProfileEdit.jsx
│   │   │   ├── Laboratory/
│   │   │   │   ├── LabDashboard.jsx
│   │   │   │   ├── UploadReports.jsx
│   │   │   │   └── Collaborations.jsx
│   │   │   ├── Admin/
│   │   │   │   ├── AdminDashboard.jsx
│   │   │   │   ├── UserApproval.jsx
│   │   │   │   ├── Statistics.jsx
│   │   │   │   └── AdminManagement.jsx
│   │   │   ├── Shared/
│   │   │   │   ├── Header.jsx
│   │   │   │   ├── Sidebar.jsx
│   │   │   │   └── Loading.jsx
│   │   │   └── Payment/
│   │   │       └── SubscriptionPortal.jsx
│   │   ├── services/
│   │   │   ├── api.js
│   │   │   ├── authService.js
│   │   │   └── prescriptionService.js
│   │   ├── context/
│   │   │   └── AuthContext.jsx
│   │   ├── utils/
│   │   │   ├── canvasUtils.js
│   │   │   └── pdfExport.js
│   │   ├── App.jsx
│   │   ├── index.jsx
│   │   └── App.css
│   ├── .env
│   ├── package.json
│   └── README.md
└── README.md
```

## Installation & Setup

### Prerequisites
- Node.js (v16 or higher)
- MongoDB
- Twilio Account
- Google OAuth Credentials
- Facebook OAuth Credentials
- Stripe Account

### Backend Setup

1. **Navigate to backend directory:**
```bash
cd backend
```

2. **Install dependencies:**
```bash
npm init -y
npm install express mongoose passport passport-google-oauth20 passport-facebook dotenv bcryptjs jsonwebtoken express-validator cors multer sharp twilio nodemailer pdfkit canvas stripe helmet express-rate-limit compression morgan
npm install --save-dev nodemon
```

3. **Create .env file:**
```env
PORT=5000
MONGODB_URI=mongodb://localhost:27017/digital-prescription
JWT_SECRET=your_super_secret_jwt_key_change_this_in_production
JWT_EXPIRE=7d

# Google OAuth
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_CALLBACK_URL=http://localhost:5000/api/auth/google/callback

# Facebook OAuth
FACEBOOK_APP_ID=your_facebook_app_id
FACEBOOK_APP_SECRET=your_facebook_app_secret
FACEBOOK_CALLBACK_URL=http://localhost:5000/api/auth/facebook/callback

# Twilio
TWILIO_ACCOUNT_SID=your_twilio_account_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TWILIO_WHATSAPP_NUMBER=whatsapp:+14155238886

# Stripe
STRIPE_SECRET_KEY=your_stripe_secret_key
STRIPE_WEBHOOK_SECRET=your_stripe_webhook_secret

# Email
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=your_email@gmail.com
EMAIL_PASS=your_email_password

# Frontend URL
FRONTEND_URL=http://localhost:3000

# File Upload
MAX_FILE_SIZE=50000000
```

4. **Update package.json scripts:**
```json
"scripts": {
  "start": "node src/server.js",
  "dev": "nodemon src/server.js"
}
```

### Frontend Setup

1. **Create React app with Ant Design:**
```bash
npx create-react-app frontend
cd frontend
npm install antd @ant-design/icons axios react-router-dom fabric jspdf html2canvas react-google-login react-facebook-login @stripe/stripe-js @stripe/react-stripe-js recharts moment
```

2. **Create .env file:**
```env
REACT_APP_API_URL=http://localhost:5000/api
REACT_APP_GOOGLE_CLIENT_ID=your_google_client_id
REACT_APP_FACEBOOK_APP_ID=your_facebook_app_id
REACT_APP_STRIPE_PUBLIC_KEY=your_stripe_public_key
```

## Backend Implementation

### File: backend/src/server.js
```javascript
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const helmet = require('helmet');
const compression = require('compression');
const rateLimit = require('express-rate-limit');
const morgan = require('morgan');
require('dotenv').config();

const authRoutes = require('./routes/auth');
const doctorRoutes = require('./routes/doctor');
const labRoutes = require('./routes/lab');
const adminRoutes = require('./routes/admin');
const prescriptionRoutes = require('./routes/prescription');
const paymentRoutes = require('./routes/payment');

const app = express();

// Security middleware
app.use(helmet());
app.use(compression());
app.use(morgan('combined'));

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
});
app.use('/api/', limiter);

// CORS
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true
}));

// Body parser
app.use(express.json({ limit: '50mb' }));
app.use(express.urlencoded({ extended: true, limit: '50mb' }));

// Static files
app.use('/uploads', express.static('uploads'));

// Database connection
mongoose.connect(process.env.MONGODB_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true
})
.then(() => console.log('MongoDB connected'))
.catch(err => console.error('MongoDB connection error:', err));

// Passport initialization
require('./config/passport');

// Routes
app.use('/api/auth', authRoutes);
app.use('/api/doctor', doctorRoutes);
app.use('/api/lab', labRoutes);
app.use('/api/admin', adminRoutes);
app.use('/api/prescription', prescriptionRoutes);
app.use('/api/payment', paymentRoutes);

// Error handling middleware
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.status || 500).json({
    success: false,
    message: err.message || 'Internal Server Error',
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
  });
});

// 404 handler
app.use((req, res) => {
  res.status(404).json({
    success: false,
    message: 'Route not found'
  });
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### File: backend/src/config/database.js
```javascript
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    const conn = await mongoose.connect(process.env.MONGODB_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true
    });
    console.log(`MongoDB Connected: ${conn.connection.host}`);
  } catch (error) {
    console.error(`Error: ${error.message}`);
    process.exit(1);
  }
};

module.exports = connectDB;
```

### File: backend/src/config/passport.js
```javascript
const passport = require('passport');
const GoogleStrategy = require('passport-google-oauth20').Strategy;
const FacebookStrategy = require('passport-facebook').Strategy;
const User = require('../models/User');

// Google OAuth Strategy
passport.use(new GoogleStrategy({
  clientID: process.env.GOOGLE_CLIENT_ID,
  clientSecret: process.env.GOOGLE_CLIENT_SECRET,
  callbackURL: process.env.GOOGLE_CALLBACK_URL
}, async (accessToken, refreshToken, profile, done) => {
  try {
    let user = await User.findOne({ googleId: profile.id });
    
    if (!user) {
      user = await User.create({
        googleId: profile.id,
        email: profile.emails[0].value,
        name: profile.displayName,
        avatar: profile.photos[0].value,
        authProvider: 'google'
      });
    }
    
    return done(null, user);
  } catch (error) {
    return done(error, null);
  }
}));

// Facebook OAuth Strategy
passport.use(new FacebookStrategy({
  clientID: process.env.FACEBOOK_APP_ID,
  clientSecret: process.env.FACEBOOK_APP_SECRET,
  callbackURL: process.env.FACEBOOK_CALLBACK_URL,
  profileFields: ['id', 'displayName', 'photos', 'email']
}, async (accessToken, refreshToken, profile, done) => {
  try {
    let user = await User.findOne({ facebookId: profile.id });
    
    if (!user) {
      user = await User.create({
        facebookId: profile.id,
        email: profile.emails ? profile.emails[0].value : null,
        name: profile.displayName,
        avatar: profile.photos ? profile.photos[0].value : null,
        authProvider: 'facebook'
      });
    }
    
    return done(null, user);
  } catch (error) {
    return done(error, null);
  }
}));

passport.serializeUser((user, done) => {
  done(null, user.id);
});

passport.deserializeUser(async (id, done) => {
  try {
    const user = await User.findById(id);
    done(null, user);
  } catch (error) {
    done(error, null);
  }
});

module.exports = passport;
```

### File: backend/src/config/twilio.js
```javascript
const twilio = require('twilio');

const client = twilio(
  process.env.TWILIO_ACCOUNT_SID,
  process.env.TWILIO_AUTH_TOKEN
);

module.exports = client;
```

## Database Models

### File: backend/src/models/User.js
```javascript
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

const userSchema = new mongoose.Schema({
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
    trim: true
  },
  password: {
    type: String,
    select: false
  },
  name: {
    type: String,
    required: true
  },
  avatar: String,
  role: {
    type: String,
    enum: ['doctor', 'laboratory', 'admin'],
    required: true
  },
  googleId: String,
  facebookId: String,
  authProvider: {
    type: String,
    enum: ['local', 'google', 'facebook'],
    default: 'local'
  },
  isApproved: {
    type: Boolean,
    default: false
  },
  isActive: {
    type: Boolean,
    default: true
  },
  subscription: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Subscription'
  }
}, {
  timestamps: true
});

userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) return next();
  this.password = await bcrypt.hash(this.password, 12);
  next();
});

userSchema.methods.comparePassword = async function(candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
};

module.exports = mongoose.model('User', userSchema);
```

### File: backend/src/models/Doctor.js
```javascript
const mongoose = require('mongoose');

const doctorSchema = new mongoose.Schema({
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },
  specialization: {
    type: String,
    required: true
  },
  qualification: {
    type: String,
    required: true
  },
  registrationNumber: {
    type: String,
    required: true,
    unique: true
  },
  experience: Number,
  hospitals: [{
    name: String,
    address: String,
    city: String,
    state: String,
    pincode: String,
    phone: String,
    logo: String
  }],
  contactNumber: String,
  signature: String,
  patients: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Patient'
  }],
  prescriptionsCount: {
    type: Number,
    default: 0
  }
}, {
  timestamps: true
});

module.exports = mongoose.model('Doctor', doctorSchema);
```

### File: backend/src/models/Laboratory.js
```javascript
const mongoose = require('mongoose');

const laboratorySchema = new mongoose.Schema({
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },
  labName: {
    type: String,
    required: true
  },
  registrationNumber: {
    type: String,
    required: true,
    unique: true
  },
  address: {
    street: String,
    city: String,
    state: String,
    pincode: String
  },
  contactNumber: String,
  servicesOffered: [String],
  collaborations: [{
    doctor: {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'Doctor'
    },
    hospital: String,
    status: {
      type: String,
      enum: ['pending', 'approved', 'rejected'],
      default: 'pending'
    },
    approvedBy: {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'Admin'
    },
    approvedAt: Date
  }],
  reportsUploaded: {
    type: Number,
    default: 0
  }
}, {
  timestamps: true
});

module.exports = mongoose.model('Laboratory', laboratorySchema);
```

### File: backend/src/models/Patient.js
```javascript
const mongoose = require('mongoose');

const patientSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  age: Number,
  gender: {
    type: String,
    enum: ['male', 'female', 'other']
  },
  contactNumber: {
    type: String,
    required: true
  },
  whatsappNumber: String,
  email: String,
  address: {
    street: String,
    city: String,
    state: String,
    pincode: String
  },
  bloodGroup: String,
  allergies: [String],
  chronicConditions: [String],
  doctor: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Doctor'
  },
  prescriptions: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Prescription'
  }],
  labReports: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'LabReport'
  }]
}, {
  timestamps: true
});

module.exports = mongoose.model('Patient', patientSchema);
```

### File: backend/src/models/Prescription.js
```javascript
const mongoose = require('mongoose');

const prescriptionSchema = new mongoose.Schema({
  doctor: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Doctor',
    required: true
  },
  patient: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Patient',
    required: true
  },
  canvasPages: [{
    pageNumber: Number,
    canvasData: String, // Base64 encoded canvas data
    drawingData: mongoose.Schema.Types.Mixed // Fabric.js JSON
  }],
  diagnosis: String,
  symptoms: [String],
  isRareCase: {
    type: Boolean,
    default: false
  },
  rareCaseDetails: String,
  pdfUrl: String,
  sentViaWhatsApp: {
    type: Boolean,
    default: false
  },
  whatsappMessageId: String,
  status: {
    type: String,
    enum: ['draft', 'completed', 'sent'],
    default: 'draft'
  }
}, {
  timestamps: true
});

module.exports = mongoose.model('Prescription', prescriptionSchema);
```

### File: backend/src/models/RareCase.js
```javascript
const mongoose = require('mongoose');

const rareCaseSchema = new mongoose.Schema({
  doctor: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Doctor',
    required: true
  },
  patient: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Patient',
    required: true
  },
  prescription: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Prescription'
  },
  title: {
    type: String,
    required: true
  },
  description: {
    type: String,
    required: true
  },
  diagnosis: String,
  treatment: String,
  outcome: String,
  images: [String],
  tags: [String]
}, {
  timestamps: true
});

module.exports = mongoose.model('RareCase', rareCaseSchema);
```

### File: backend/src/models/Note.js
```javascript
const mongoose = require('mongoose');

const noteSchema = new mongoose.Schema({
  doctor: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Doctor',
    required: true
  },
  patient: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Patient'
  },
  title: {
    type: String,
    required: true
  },
  content: {
    type: String,
    required: true
  },
  category: {
    type: String,
    enum: ['general', 'patient', 'research', 'reminder'],
    default: 'general'
  },
  tags: [String],
  isPinned: {
    type: Boolean,
    default: false
  }
}, {
  timestamps: true
});

module.exports = mongoose.model('Note', noteSchema);
```

### File: backend/src/models/LabReport.js
```javascript
const mongoose = require('mongoose');

const labReportSchema = new mongoose.Schema({
  laboratory: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Laboratory',
    required: true
  },
  patient: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Patient',
    required: true
  },
  doctor: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Doctor',
    required: true
  },
  reportType: {
    type: String,
    required: true,
    enum: ['xray', 'mri', 'ct-scan', 'ultrasound', 'blood-test', 'urine-test', 'ecg', 'eeg', 'biopsy', 'other']
  },
  reportName: String,
  description: String,
  files: [{
    originalName: String,
    fileName: String,
    fileType: String, // 'image' or 'pdf'
    fileUrl: String,
    compressedUrl: String,
    fileSize: Number
  }],
  testDate: Date,
  uploadDate: {
    type: Date,
    default: Date.now
  }
}, {
  timestamps: true
});

module.exports = mongoose.model('LabReport', labReportSchema);
```

### File: backend/src/models/Subscription.js
```javascript
const mongoose = require('mongoose');

const subscriptionSchema = new mongoose.Schema({
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },
  plan: {
    type: String,
    enum: ['basic', 'premium'],
    default: 'basic'
  },
  amount: {
    type: Number,
    required: true
  },
  prescriptionLimit: Number, // null for unlimited
  prescriptionCount: {
    type: Number,
    default: 0
  },
  startDate: {
    type: Date,
    default: Date.now
  },
  endDate: {
    type: Date,
    required: true
  },
  stripeSubscriptionId: String,
  stripeCustomerId: String,
  status: {
    type: String,
    enum: ['active', 'cancelled', 'expired'],
    default: 'active'
  },
  autoRenew: {
    type: Boolean,
    default: true
  }
}, {
  timestamps: true
});

module.exports = mongoose.model('Subscription', subscriptionSchema);
```

### File: backend/src/models/Admin.js
```javascript
const mongoose = require('mongoose');

const adminSchema = new mongoose.Schema({
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },
  permissions: [{
    type: String,
    enum: ['approve_users', 'manage_admins', 'view_analytics', 'manage_subscriptions']
  }],
  isSuperAdmin: {
    type: Boolean,
    default: false
  }
}, {
  timestamps: true
});

module.exports = mongoose.model('Admin', adminSchema);
```

### File: backend/src/models/Collaboration.js
```javascript
const mongoose = require('mongoose');

const collaborationSchema = new mongoose.Schema({
  doctor: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Doctor',
    required: true
  },
  laboratory: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Laboratory',
    required: true
  },
  hospital: String,
  status: {
    type: String,
    enum: ['pending', 'approved', 'rejected'],
    default: 'pending'
  },
  requestedBy: {
    type: String,
    enum: ['doctor', 'laboratory']
  },
  approvedBy: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Admin'
  },
  approvedAt: Date,
  rejectedReason: String
}, {
  timestamps: true
});

module.exports = mongoose.model('Collaboration', collaborationSchema);
```

## Middleware

### File: backend/src/middleware/auth.js
```javascript
const jwt = require('jsonwebtoken');
const User = require('../models/User');

exports.protect = async (req, res, next) => {
  try {
    let token;
    
    if (req.headers.authorization && req.headers.authorization.startsWith('Bearer')) {
      token = req.headers.authorization.split(' ')[1];
    }
    
    if (!token) {
      return res.status(401).json({
        success: false,
        message: 'Not authorized to access this route'
      });
    }
    
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = await User.findById(decoded.id);
    
    if (!req.user) {
      return res.status(401).json({
        success: false,
        message: 'User not found'
      });
    }
    
    if (!req.user.isActive) {
      return res.status(401).json({
        success: false,
        message: 'Account is deactivated'
      });
    }
    
    next();
  } catch (error) {
    return res.status(401).json({
      success: false,
      message: 'Not authorized to access this route'
    });
  }
};

exports.checkApproval = (req, res, next) => {
  if (!req.user.isApproved) {
    return res.status(403).json({
      success: false,
      message: 'Your account is pending approval from admin'
    });
  }
  next();
};

exports.checkSubscription = async (req, res, next) => {
  try {
    const Subscription = require('../models/Subscription');
    const subscription = await Subscription.findOne({
      user: req.user._id,
      status: 'active',
      endDate: { $gte: new Date() }
    });
    
    if (!subscription) {
      return res.status(403).json({
        success: false,
        message: 'No active subscription. Please subscribe to continue.'
      });
    }
    
    if (subscription.prescriptionLimit && subscription.prescriptionCount >= subscription.prescriptionLimit) {
      return res.status(403).json({
        success: false,
        message: 'Prescription limit reached. Please upgrade your plan.'
      });
    }
    
    req.subscription = subscription;
    next();
  } catch (error) {
    next(error);
  }
};
```

### File: backend/src/middleware/roleCheck.js
```javascript
exports.authorize = (...roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({
        success: false,
        message: 'User role not authorized to access this route'
      });
    }
    next();
  };
};
```

### File: backend/src/middleware/uploadHandler.js
```javascript
const multer = require('multer');
const path = require('path');

const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, 'uploads/');
  },
  filename: (req, file, cb) => {
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
    cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
  }
});

const fileFilter = (req, file, cb) => {
  const allowedTypes = /jpeg|jpg|png|pdf|dicom/;
  const extname = allowedTypes.test(path.extname(file.originalname).toLowerCase());
  const mimetype = allowedTypes.test(file.mimetype);
  
  if (mimetype && extname) {
    return cb(null, true);
  } else {
    cb(new Error('Only images and PDF files are allowed'));
  }
};

exports.upload = multer({
  storage: storage,
  limits: {
    fileSize: parseInt(process.env.MAX_FILE_SIZE)
  },
  fileFilter: fileFilter
});
```

## Utilities

### File: backend/src/utils/pdfGenerator.js
```javascript
const PDFDocument = require('pdfkit');
const fs = require('fs');
const path = require('path');

exports.generatePrescriptionPDF = async (prescription, doctor, patient, canvasImages) => {
  return new Promise((resolve, reject) => {
    try {
      const fileName = `prescription-${prescription._id}-${Date.now()}.pdf`;
      const filePath = path.join(__dirname, '../../uploads/prescriptions', fileName);
      
      // Ensure directory exists
      const dir = path.dirname(filePath);
      if (!fs.existsSync(dir)) {
        fs.mkdirSync(dir, { recursive: true });
      }
      
      const doc = new PDFDocument({ size: 'A4', margin: 50 });
      const writeStream = fs.createWriteStream(filePath);
      
      doc.pipe(writeStream);
      
      // Page 1: Hospital Details
      doc.fontSize(25).text('Medical Prescription', { align: 'center' });
      doc.moveDown();
      
      // Hospital/Doctor Info
      if (doctor.hospitals && doctor.hospitals[0]) {
        const hospital = doctor.hospitals[0];
        
        if (hospital.logo) {
          doc.image(hospital.logo, 50, 50, { width: 100 });
        }
        
        doc.fontSize(18).text(hospital.name, { align: 'center' });
        doc.fontSize(12).text(hospital.address, { align: 'center' });
        doc.text(`${hospital.city}, ${hospital.state} - ${hospital.pincode}`, { align: 'center' });
        doc.text(`Phone: ${hospital.phone}`, { align: 'center' });
      }
      
      doc.moveDown(2);
      
      // Doctor Info
      doc.fontSize(14).text(`Dr. ${doctor.user.name}`, { underline: true });
      doc.fontSize(11).text(`${doctor.qualification} - ${doctor.specialization}`);
      doc.text(`Reg. No: ${doctor.registrationNumber}`);
      
      doc.moveDown();
      
      // Patient Info
      doc.fontSize(14).text('Patient Details:', { underline: true });
      doc.fontSize(11).text(`Name: ${patient.name}`);
      doc.text(`Age: ${patient.age} | Gender: ${patient.gender}`);
      doc.text(`Contact: ${patient.contactNumber}`);
      if (patient.bloodGroup) {
        doc.text(`Blood Group: ${patient.bloodGroup}`);
      }
      
      doc.moveDown();
      doc.fontSize(10).text(`Date: ${new Date().toLocaleDateString()}`, { align: 'right' });
      
      // Canvas Pages (Page 2 onwards)
      for (let i = 0; i < canvasImages.length; i++) {
        doc.addPage();
        doc.image(canvasImages[i], 50, 50, {
          fit: [500, 700],
          align: 'center'
        });
      }
      
      // Last Page: Signature
      doc.addPage();
      doc.moveDown(20);
      
      if (doctor.signature) {
        doc.image(doctor.signature, 400, 650, { width: 150 });
      }
      
      doc.fontSize(11).text('Doctor\'s Signature', 400, 720);
      doc.text(`Dr. ${doctor.user.name}`, 400, 735);
      doc.text(doctor.registrationNumber, 400, 750);
      
      doc.end();
      
      writeStream.on('finish', () => {
        resolve(`/uploads/prescriptions/${fileName}`);
      });
      
      writeStream.on('error', (error) => {
        reject(error);
      });
      
    } catch (error) {
      reject(error);
    }
  });
};
```

### File: backend/src/utils/imageCompressor.js
```javascript
const sharp = require('sharp');
const path = require('path');
const fs = require('fs');

exports.compressImage = async (inputPath, outputPath = null, quality = 70) => {
  try {
    if (!outputPath) {
      const ext = path.extname(inputPath);
      const name = path.basename(inputPath, ext);
      const dir = path.dirname(inputPath);
      outputPath = path.join(dir, `${name}-compressed${ext}`);
    }
    
    await sharp(inputPath)
      .jpeg({ quality: quality, progressive: true })
      .toFile(outputPath);
    
    return outputPath;
  } catch (error) {
    throw new Error(`Image compression failed: ${error.message}`);
  }
};

exports.convertToWebP = async (inputPath, outputPath = null) => {
  try {
    if (!outputPath) {
      const name = path.basename(inputPath, path.extname(inputPath));
      const dir = path.dirname(inputPath);
      outputPath = path.join(dir, `${name}.webp`);
    }
    
    await sharp(inputPath)
      .webp({ quality: 80 })
      .toFile(outputPath);
    
    return outputPath;
  } catch (error) {
    throw new Error(`WebP conversion failed: ${error.message}`);
  }
};

exports.resizeImage = async (inputPath, width, height, outputPath = null) => {
  try {
    if (!outputPath) {
      const ext = path.extname(inputPath);
      const name = path.basename(inputPath, ext);
      const dir = path.dirname(inputPath);
      outputPath = path.join(dir, `${name}-resized${ext}`);
    }
    
    await sharp(inputPath)
      .resize(width, height, {
        fit: 'inside',
        withoutEnlargement: true
      })
      .toFile(outputPath);
    
    return outputPath;
  } catch (error) {
    throw new Error(`Image resize failed: ${error.message}`);
  }
};
```

### File: backend/src/utils/twilioService.js
```javascript
const twilioClient = require('../config/twilio');

exports.sendWhatsAppMessage = async (to, message, mediaUrl = null) => {
  try {
    const messageOptions = {
      from: process.env.TWILIO_WHATSAPP_NUMBER,
      to: `whatsapp:${to}`,
      body: message
    };
    
    if (mediaUrl) {
      messageOptions.mediaUrl = [mediaUrl];
    }
    
    const result = await twilioClient.messages.create(messageOptions);
    
    return {
      success: true,
      messageId: result.sid,
      status: result.status
    };
  } catch (error) {
    console.error('WhatsApp send error:', error);
    throw new Error(`Failed to send WhatsApp message: ${error.message}`);
  }
};

exports.sendPrescriptionViaWhatsApp = async (patientPhone, pdfUrl, doctorName) => {
  try {
    const message = `Hello! Your prescription from Dr. ${doctorName} is ready. Please find the attached PDF document.`;
    
    const fullPdfUrl = `${process.env.BACKEND_URL}${pdfUrl}`;
    
    return await exports.sendWhatsAppMessage(patientPhone, message, fullPdfUrl);
  } catch (error) {
    throw error;
  }
};
```

### File: backend/src/utils/emailService.js
```javascript
const nodemailer = require('nodemailer');

const transporter = nodemailer.createTransport({
  host: process.env.EMAIL_HOST,
  port: process.env.EMAIL_PORT,
  auth: {
    user: process.env.EMAIL_USER,
    pass: process.env.EMAIL_PASS
  }
});

exports.sendEmail = async (to, subject, html) => {
  try {
    const mailOptions = {
      from: process.env.EMAIL_USER,
      to: to,
      subject: subject,
      html: html
    };
    
    const info = await transporter.sendMail(mailOptions);
    return info;
  } catch (error) {
    console.error('Email send error:', error);
    throw error;
  }
};

exports.sendApprovalEmail = async (user, approved) => {
  const subject = approved ? 'Account Approved' : 'Account Rejected';
  const message = approved 
    ? 'Your account has been approved. You can now login and use the platform.'
    : 'Your account registration has been rejected. Please contact support for more information.';
  
  const html = `
    <h2>${subject}</h2>
    <p>Hello ${user.name},</p>
    <p>${message}</p>
    <p>Best regards,<br/>Digital Prescription Team</p>
  `;
  
  return await exports.sendEmail(user.email, subject, html);
};
```

## Controllers

### File: backend/src/controllers/authController.js
```javascript
const jwt = require('jsonwebtoken');
const User = require('../models/User');
const Doctor = require('../models/Doctor');
const Laboratory = require('../models/Laboratory');
const Admin = require('../models/Admin');

const generateToken = (id) => {
  return jwt.sign({ id }, process.env.JWT_SECRET, {
    expiresIn: process.env.JWT_EXPIRE
  });
};

exports.register = async (req, res, next) => {
  try {
    const { email, password, name, role, ...additionalData } = req.body;
    
    // Check if user exists
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(400).json({
        success: false,
        message: 'Email already registered'
      });
    }
    
    // Create user
    const user = await User.create({
      email,
      password,
      name,
      role,
      isApproved: role === 'admin' ? true : false
    });
    
    // Create role-specific profile
    if (role === 'doctor') {
      await Doctor.create({
        user: user._id,
        ...additionalData
      });
    } else if (role === 'laboratory') {
      await Laboratory.create({
        user: user._id,
        ...additionalData
      });
    } else if (role === 'admin') {
      await Admin.create({
        user: user._id,
        permissions: ['approve_users', 'view_analytics']
      });
    }
    
    const token = generateToken(user._id);
    
    res.status(201).json({
      success: true,
      token,
      user: {
        id: user._id,
        email: user.email,
        name: user.name,
        role: user.role,
        isApproved: user.isApproved
      }
    });
  } catch (error) {
    next(error);
  }
};

exports.login = async (req, res, next) => {
  try {
    const { email, password } = req.body;
    
    if (!email || !password) {
      return res.status(400).json({
        success: false,
        message: 'Please provide email and password'
      });
    }
    
    const user = await User.findOne({ email }).select('+password');
    
    if (!user || !(await user.comparePassword(password))) {
      return res.status(401).json({
        success: false,
        message: 'Invalid credentials'
      });
    }
    
    if (!user.isActive) {
      return res.status(401).json({
        success: false,
        message: 'Account is deactivated'
      });
    }
    
    const token = generateToken(user._id);
    
    res.status(200).json({
      success: true,
      token,
      user: {
        id: user._id,
        email: user.email,
        name: user.name,
        role: user.role,
        isApproved: user.isApproved
      }
    });
  } catch (error) {
    next(error);
  }
};

exports.googleAuth = async (req, res, next) => {
  try {
    const { googleId, email, name, avatar, role, ...additionalData } = req.body;
    
    let user = await User.findOne({ googleId });
    
    if (!user) {
      user = await User.create({
        googleId,
        email,
        name,
        avatar,
        role,
        authProvider: 'google',
        isApproved: role === 'admin' ? true : false
      });
      
      // Create role-specific profile
      if (role === 'doctor') {
        await Doctor.create({
          user: user._id,
          ...additionalData
        });
      } else if (role === 'laboratory') {
        await Laboratory.create({
          user: user._id,
          ...additionalData
        });
      }
    }
    
    const token = generateToken(user._id);
    
    res.status(200).json({
      success: true,
      token,
      user: {
        id: user._id,
        email: user.email,
        name: user.name,
        role: user.role,
        isApproved: user.isApproved
      }
    });
  } catch (error) {
    next(error);
  }
};

exports.facebookAuth = async (req, res, next) => {
  try {
    const { facebookId, email, name, avatar, role, ...additionalData } = req.body;
    
    let user = await User.findOne({ facebookId });
    
    if (!user) {
      user = await User.create({
        facebookId,
        email,
        name,
        avatar,
        role,
        authProvider: 'facebook',
        isApproved: role === 'admin' ? true : false
      });
      
      // Create role-specific profile
      if (role === 'doctor') {
        await Doctor.create({
          user: user._id,
          ...additionalData
        });
      } else if (role === 'laboratory') {
        await Laboratory.create({
          user: user._id,
          ...additionalData
        });
      }
    }
    
    const token = generateToken(user._id);
    
    res.status(200).json({
      success: true,
      token,
      user: {
        id: user._id,
        email: user.email,
        name: user.name,
        role: user.role,
        isApproved: user.isApproved
      }
    });
  } catch (error) {
    next(error);
  }
};

exports.getMe = async (req, res, next) => {
  try {
    const user = await User.findById(req.user._id);
    
    let profile;
    if (user.role === 'doctor') {
      profile = await Doctor.findOne({ user: user._id }).populate('user');
    } else if (user.role === 'laboratory') {
      profile = await Laboratory.findOne({ user: user._id }).populate('user');
    } else if (user.role === 'admin') {
      profile = await Admin.findOne({ user: user._id }).populate('user');
    }
    
    res.status(200).json({
      success: true,
      user,
      profile
    });
  } catch (error) {
    next(error);
  }
};
```

### File: backend/src/controllers/doctorController.js
```javascript
const Doctor = require('../models/Doctor');
const Patient = require('../models/Patient');
const Prescription = require('../models/Prescription');
const Note = require('../models/Note');
const RareCase = require('../models/RareCase');
const LabReport = require('../models/LabReport');

exports.getDashboard = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id });
    
    const totalPatients = await Patient.countDocuments({ doctor: doctor._id });
    const totalPrescriptions = await Prescription.countDocuments({ doctor: doctor._id });
    const rareCases = await RareCase.countDocuments({ doctor: doctor._id });
    const recentPrescriptions = await Prescription.find({ doctor: doctor._id })
      .populate('patient')
      .sort('-createdAt')
      .limit(10);
    
    // Statistics for charts
    const last30Days = new Date();
    last30Days.setDate(last30Days.getDate() - 30);
    
    const prescriptionStats = await Prescription.aggregate([
      {
        $match: {
          doctor: doctor._id,
          createdAt: { $gte: last30Days }
        }
      },
      {
        $group: {
          _id: { $dateToString: { format: "%Y-%m-%d", date: "$createdAt" } },
          count: { $sum: 1 }
        }
      },
      { $sort: { _id: 1 } }
    ]);
    
    res.status(200).json({
      success: true,
      data: {
        totalPatients,
        totalPrescriptions,
        rareCases,
        recentPrescriptions,
        prescriptionStats
      }
    });
  } catch (error) {
    next(error);
  }
};

exports.updateProfile = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOneAndUpdate(
      { user: req.user._id },
      req.body,
      { new: true, runValidators: true }
    ).populate('user');
    
    res.status(200).json({
      success: true,
      data: doctor
    });
  } catch (error) {
    next(error);
  }
};

exports.uploadSignature = async (req, res, next) => {
  try {
    if (!req.file) {
      return res.status(400).json({
        success: false,
        message: 'Please upload a signature image'
      });
    }
    
    const doctor = await Doctor.findOneAndUpdate(
      { user: req.user._id },
      { signature: `/uploads/${req.file.filename}` },
      { new: true }
    );
    
    res.status(200).json({
      success: true,
      data: doctor
    });
  } catch (error) {
    next(error);
  }
};

exports.getPatients = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id });
    const patients = await Patient.find({ doctor: doctor._id })
      .sort('-createdAt');
    
    res.status(200).json({
      success: true,
      count: patients.length,
      data: patients
    });
  } catch (error) {
    next(error);
  }
};

exports.getPatientDetails = async (req, res, next) => {
  try {
    const patient = await Patient.findById(req.params.id)
      .populate('prescriptions')
      .populate({
        path: 'labReports',
        populate: { path: 'laboratory' }
      });
    
    if (!patient) {
      return res.status(404).json({
        success: false,
        message: 'Patient not found'
      });
    }
    
    res.status(200).json({
      success: true,
      data: patient
    });
  } catch (error) {
    next(error);
  }
};

exports.createNote = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id });
    
    const note = await Note.create({
      doctor: doctor._id,
      ...req.body
    });
    
    res.status(201).json({
      success: true,
      data: note
    });
  } catch (error) {
    next(error);
  }
};

exports.getNotes = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id });
    const notes = await Note.find({ doctor: doctor._id })
      .sort('-createdAt');
    
    res.status(200).json({
      success: true,
      count: notes.length,
      data: notes
    });
  } catch (error) {
    next(error);
  }
};

exports.updateNote = async (req, res, next) => {
  try {
    const note = await Note.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true, runValidators: true }
    );
    
    if (!note) {
      return res.status(404).json({
        success: false,
        message: 'Note not found'
      });
    }
    
    res.status(200).json({
      success: true,
      data: note
    });
  } catch (error) {
    next(error);
  }
};

exports.deleteNote = async (req, res, next) => {
  try {
    const note = await Note.findByIdAndDelete(req.params.id);
    
    if (!note) {
      return res.status(404).json({
        success: false,
        message: 'Note not found'
      });
    }
    
    res.status(200).json({
      success: true,
      message: 'Note deleted successfully'
    });
  } catch (error) {
    next(error);
  }
};

exports.getRareCases = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id });
    const rareCases = await RareCase.find({ doctor: doctor._id })
      .populate('patient')
      .sort('-createdAt');
    
    res.status(200).json({
      success: true,
      count: rareCases.length,
      data: rareCases
    });
  } catch (error) {
    next(error);
  }
};

exports.createRareCase = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id });
    
    const rareCase = await RareCase.create({
      doctor: doctor._id,
      ...req.body
    });
    
    res.status(201).json({
      success: true,
      data: rareCase
    });
  } catch (error) {
    next(error);
  }
};
```

### File: backend/src/controllers/prescriptionController.js
```javascript
const Prescription = require('../models/Prescription');
const Patient = require('../models/Patient');
const Doctor = require('../models/Doctor');
const Subscription = require('../models/Subscription');
const { generatePrescriptionPDF } = require('../utils/pdfGenerator');
const { sendPrescriptionViaWhatsApp } = require('../utils/twilioService');

exports.createPrescription = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id }).populate('user');
    const { patientId, canvasPages, diagnosis, symptoms, isRareCase, rareCaseDetails } = req.body;
    
    let patient = await Patient.findById(patientId);
    
    if (!patient) {
      return res.status(404).json({
        success: false,
        message: 'Patient not found'
      });
    }
    
    const prescription = await Prescription.create({
      doctor: doctor._id,
      patient: patient._id,
      canvasPages,
      diagnosis,
      symptoms,
      isRareCase,
      rareCaseDetails,
      status: 'draft'
    });
    
    // Add prescription to patient
    patient.prescriptions.push(prescription._id);
    await patient.save();
    
    res.status(201).json({
      success: true,
      data: prescription
    });
  } catch (error) {
    next(error);
  }
};

exports.updatePrescription = async (req, res, next) => {
  try {
    const prescription = await Prescription.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true, runValidators: true }
    );
    
    if (!prescription) {
      return res.status(404).json({
        success: false,
        message: 'Prescription not found'
      });
    }
    
    res.status(200).json({
      success: true,
      data: prescription
    });
  } catch (error) {
    next(error);
  }
};

exports.completePrescription = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id }).populate('user');
    const prescription = await Prescription.findById(req.params.id)
      .populate('patient');
    
    if (!prescription) {
      return res.status(404).json({
        success: false,
        message: 'Prescription not found'
      });
    }
    
    // Generate PDF
    const canvasImages = req.body.canvasImages; // Base64 images from frontend
    const pdfUrl = await generatePrescriptionPDF(prescription, doctor, prescription.patient, canvasImages);
    
    prescription.pdfUrl = pdfUrl;
    prescription.status = 'completed';
    await prescription.save();
    
    // Update doctor's prescription count
    doctor.prescriptionsCount += 1;
    await doctor.save();
    
    // Update subscription count
    if (req.subscription) {
      req.subscription.prescriptionCount += 1;
      await req.subscription.save();
    }
    
    res.status(200).json({
      success: true,
      data: prescription
    });
  } catch (error) {
    next(error);
  }
};

exports.sendPrescription = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id }).populate('user');
    const prescription = await Prescription.findById(req.params.id)
      .populate('patient');
    
    if (!prescription) {
      return res.status(404).json({
        success: false,
        message: 'Prescription not found'
      });
    }
    
    if (!prescription.pdfUrl) {
      return res.status(400).json({
        success: false,
        message: 'Please complete the prescription first'
      });
    }
    
    const patient = prescription.patient;
    const whatsappNumber = patient.whatsappNumber || patient.contactNumber;
    
    // Send via WhatsApp
    const result = await sendPrescriptionViaWhatsApp(
      whatsappNumber,
      prescription.pdfUrl,
      doctor.user.name
    );
    
    prescription.sentViaWhatsApp = true;
    prescription.whatsappMessageId = result.messageId;
    prescription.status = 'sent';
    await prescription.save();
    
    res.status(200).json({
      success: true,
      message: 'Prescription sent successfully',
      data: prescription
    });
  } catch (error) {
    next(error);
  }
};

exports.getPrescriptions = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id });
    const prescriptions = await Prescription.find({ doctor: doctor._id })
      .populate('patient')
      .sort('-createdAt');
    
    res.status(200).json({
      success: true,
      count: prescriptions.length,
      data: prescriptions
    });
  } catch (error) {
    next(error);
  }
};

exports.getPrescriptionById = async (req, res, next) => {
  try {
    const prescription = await Prescription.findById(req.params.id)
      .populate('patient')
      .populate({
        path: 'doctor',
        populate: { path: 'user' }
      });
    
    if (!prescription) {
      return res.status(404).json({
        success: false,
        message: 'Prescription not found'
      });
    }
    
    res.status(200).json({
      success: true,
      data: prescription
    });
  } catch (error) {
    next(error);
  }
};

exports.deletePrescription = async (req, res, next) => {
  try {
    const prescription = await Prescription.findByIdAndDelete(req.params.id);
    
    if (!prescription) {
      return res.status(404).json({
        success: false,
        message: 'Prescription not found'
      });
    }
    
    res.status(200).json({
      success: true,
      message: 'Prescription deleted successfully'
    });
  } catch (error) {
    next(error);
  }
};

exports.createPatient = async (req, res, next) => {
  try {
    const doctor = await Doctor.findOne({ user: req.user._id });
    
    const patient = await Patient.create({
      ...req.body,
      doctor: doctor._id
    });
    
    doctor.patients.push(patient._id);
    await doctor.save();
    
    res.status(201).json({
      success: true,
      data: patient
    });
  } catch (error) {
    next(error);
  }
};
```

### File: backend/src/controllers/labController.js
```javascript
const Laboratory = require('../models/Laboratory');
const LabReport = require('../models/LabReport');
const Patient = require('../models/Patient');
const Doctor = require('../models/Doctor');
const Collaboration = require('../models/Collaboration');
const { compressImage, resizeImage } = require('../utils/imageCompressor');
const path = require('path');

exports.getDashboard = async (req, res, next) => {
  try {
    const lab = await Laboratory.findOne({ user: req.user._id });
    
    const totalReports = await LabReport.countDocuments({ laboratory: lab._id });
    const activeCollaborations = await Collaboration.countDocuments({
      laboratory: lab._id,
      status: 'approved'
    });
    
    const recentReports = await LabReport.find({ laboratory: lab._id })
      .populate('patient')
      .populate('doctor')
      .sort('-createdAt')
      .limit(10);
    
    // Statistics
    const last30Days = new Date();
    last30Days.setDate(last30Days.getDate() - 30);
    
    const reportStats = await LabReport.aggregate([
      {
        $match: {
          laboratory: lab._id,
          createdAt: { $gte: last30Days }
        }
      },
      {
        $group: {
          _id: { $dateToString: { format: "%Y-%m-%d", date: "$createdAt" } },
          count: { $sum: 1 }
        }
      },
      { $sort: { _id: 1 } }
    ]);
    
    const reportTypeDistribution = await LabReport.aggregate([
      { $match: { laboratory: lab._id } },
      {
        $group: {
          _id: '$reportType',
          count: { $sum: 1 }
        }
      }
    ]);
    
    res.status(200).json({
      success: true,
      data: {
        totalReports,
        activeCollaborations,
        recentReports,
        reportStats,
        reportTypeDistribution
      }
    });
  } catch (error) {
    next(error);
  }
};

exports.updateProfile = async (req, res, next) => {
  try {
    const lab = await Laboratory.findOneAndUpdate(
      { user: req.user._id },
      req.body,
      { new: true, runValidators: true }
    ).populate('user');
    
    res.status(200).json({
      success: true,
      data: lab
    });
  } catch (error) {
    next(error);
  }
};

exports.uploadReport = async (req, res, next) => {
  try {
    const lab = await Laboratory.findOne({ user: req.user._id });
    const { patientId, doctorId, reportType, reportName, description, testDate } = req.body;
    
    if (!req.files || req.files.length === 0) {
      return res.status(400).json({
        success: false,
        message: 'Please upload at least one file'
      });
    }
    
    const patient = await Patient.findById(patientId);
    if (!patient) {
      return res.status(404).json({
        success: false,
        message: 'Patient not found'
      });
    }
    
    const processedFiles = [];
    
    for (const file of req.files) {
      const fileType = file.mimetype.startsWith('image/') ? 'image' : 'pdf';
      let compressedUrl = null;
      
      if (fileType === 'image') {
        // Compress and resize large medical images
        const compressedPath = await compressImage(file.path, null, 70);
        const resizedPath = await resizeImage(compressedPath, 1920, 1080);
        compressedUrl = `/uploads/${path.basename(resizedPath)}`;
      }
      
      processedFiles.push({
        originalName: file.originalname,
        fileName: file.filename,
        fileType,
        fileUrl: `/uploads/${file.filename}`,
        compressedUrl,
        fileSize: file.size
      });
    }
    
    const labReport = await LabReport.create({
      laboratory: lab._id,
      patient: patientId,
      doctor: doctorId,
      reportType,
      reportName,
      description,
      testDate,
      files: processedFiles
    });
    
    // Add report to patient
    patient.labReports.push(labReport._id);
    await patient.save();
    
    // Update lab stats
    lab.reportsUploaded += 1;
    await lab.save();
    
    res.status(201).json({
      success: true,
      data: labReport
    });
  } catch (error) {
    next(error);
  }
};

exports.getReports = async (req, res, next) => {
  try {
    const lab = await Laboratory.findOne({ user: req.user._id });
    const { patientId, doctorId, reportType } = req.query;
    
    const filter = { laboratory: lab._id };
    if (patientId) filter.patient = patientId;
    if (doctorId) filter.doctor = doctorId;
    if (reportType) filter.reportType = reportType;
    
    const reports = await LabReport.find(filter)
      .populate('patient')
      .populate('doctor')
      .sort('-createdAt');
    
    res.status(200).json({
      success: true,
      count: reports.length,
      data: reports
    });
  } catch (error) {
    next(error);
  }
};

exports.requestCollaboration = async (req, res, next) => {
  try {
    const lab = await Laboratory.findOne({ user: req.user._id });
    const { doctorId, hospital } = req.body;
    
    const existingCollaboration = await Collaboration.findOne({
      laboratory: lab._id,
      doctor: doctorId,
      status: { $in: ['pending', 'approved'] }
    });
    
    if (existingCollaboration) {
      return res.status(400).json({
        success: false,
        message: 'Collaboration request already exists'
      });
    }
    
    const collaboration = await Collaboration.create({
      laboratory: lab._id,
      doctor: doctorId,
      hospital,
      requestedBy: 'laboratory'
    });
    
    res.status(201).json({
      success: true,
      data: collaboration
    });
  } catch (error) {
    next(error);
  }
};

exports.getCollaborations = async (req, res, next) => {
  try {
    const lab = await Laboratory.findOne({ user: req.user._id });
    const collaborations = await Collaboration.find({ laboratory: lab._id })
      .populate({
        path: 'doctor',
        populate: { path: 'user' }
      })
      .sort('-createdAt');
    
    res.status(200).json({
      success: true,
      count: collaborations.length,
      data: collaborations
    });
  } catch (error) {
    next(error);
  }
};

exports.getPatients = async (req, res, next) => {
  try {
    const lab = await Laboratory.findOne({ user: req.user._id });
    
    // Get all patients from approved collaborations
    const collaborations = await Collaboration.find({
      laboratory: lab._id,
      status: 'approved'
    }).populate('doctor');
    
    const doctorIds = collaborations.map(c => c.doctor._id);
    
    const patients = await Patient.find({
      doctor: { $in: doctorIds }
    }).populate('doctor');
    
    res.status(200).json({
      success: true,
      count: patients.length,
      data: patients
    });
  } catch (error) {
    next(error);
  }
};
```

### File: backend/src/controllers/adminController.js
```javascript
const User = require('../models/User');
const Doctor = require('../models/Doctor');
const Laboratory = require('../models/Laboratory');
const Admin = require('../models/Admin');
const Prescription = require('../models/Prescription');
const LabReport = require('../models/LabReport');
const Subscription = require('../models/Subscription');
const Collaboration = require('../models/Collaboration');
const { sendApprovalEmail } = require('../utils/emailService');

exports.getDashboard = async (req, res, next) => {
  try {
    const totalUsers = await User.countDocuments();
    const totalDoctors = await Doctor.countDocuments();
    const totalLabs = await Laboratory.countDocuments();
    const pendingApprovals = await User.countDocuments({ isApproved: false });
    
    const totalPrescriptions = await Prescription.countDocuments();
    const totalLabReports = await LabReport.countDocuments();
    
    const activeSubscriptions = await Subscription.countDocuments({
      status: 'active',
      endDate: { $gte: new Date() }
    });
    
    // Revenue calculation
    const revenueData = await Subscription.aggregate([
      {
        $match: {
          status: 'active'
        }
      },
      {
        $group: {
          _id: null,
          totalRevenue: { $sum: '$amount' },
          avgRevenue: { $avg: '$amount' }
        }
      }
    ]);
    
    // Usage trends
    const last30Days = new Date();
    last30Days.setDate(last30Days.getDate() - 30);
    
    const prescriptionTrends = await Prescription.aggregate([
      {
        $match: { createdAt: { $gte: last30Days } }
      },
      {
        $group: {
          _id: { $dateToString: { format: "%Y-%m-%d", date: "$createdAt" } },
          count: { $sum: 1 }
        }
      },
      { $sort: { _id: 1 } }
    ]);
    
    const labReportTrends = await LabReport.aggregate([
      {
        $match: { createdAt: { $gte: last30Days } }
      },
      {
        $group: {
          _id: { $dateToString: { format: "%Y-%m-%d", date: "$createdAt" } },
          count: { $sum: 1 }
        }
      },
      { $sort: { _id: 1 } }
    ]);
    
    const userRegistrationTrends = await User.aggregate([
      {
        $match: { createdAt: { $gte: last30Days } }
      },
      {
        $group: {
          _id: { $dateToString: { format: "%Y-%m-%d", date: "$createdAt" } },
          count: { $sum: 1 }
        }
      },
      { $sort: { _id: 1 } }
    ]);
    
    res.status(200).json({
      success: true,
      data: {
        totalUsers,
        totalDoctors,
        totalLabs,
        pendingApprovals,
        totalPrescriptions,
        totalLabReports,
        activeSubscriptions,
        revenue: revenueData[0] || { totalRevenue: 0, avgRevenue: 0 },
        trends: {
          prescriptions: prescriptionTrends,
          labReports: labReportTrends,
          userRegistrations: userRegistrationTrends
        }
      }
    });
  } catch (error) {
    next(error);
  }
};

exports.getPendingApprovals = async (req, res, next) => {
  try {
    const pendingUsers = await User.find({ isApproved: false })
      .sort('-createdAt');
    
    const usersWithProfiles = await Promise.all(
      pendingUsers.map(async (user) => {
        let profile;
        if (user.role === 'doctor') {
          profile = await Doctor.findOne({ user: user._id });
        } else if (user.role === 'laboratory') {
          profile = await Laboratory.findOne({ user: user._id });
        }
        return { user, profile };
      })
    );
    
    res.status(200).json({
      success: true,
      count: usersWithProfiles.length,
      data: usersWithProfiles
    });
  } catch (error) {
    next(error);
  }
};

exports.approveUser = async (req, res, next) => {
  try {
    const user = await User.findByIdAndUpdate(
      req.params.userId,
      { isApproved: true },
      { new: true }
    );
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    // Send approval email
    await sendApprovalEmail(user, true);
    
    res.status(200).json({
      success: true,
      message: 'User approved successfully',
      data: user
    });
  } catch (error) {
    next(error);
  }
};

exports.rejectUser = async (req, res, next) => {
  try {
    const user = await User.findByIdAndUpdate(
      req.params.userId,
      { isActive: false },
      { new: true }
    );
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    // Send rejection email
    await sendApprovalEmail(user, false);
    
    res.status(200).json({
      success: true,
      message: 'User rejected successfully',
      data: user
    });
  } catch (error) {
    next(error);
  }
};

exports.getAllUsers = async (req, res, next) => {
  try {
    const { role, isApproved, isActive } = req.query;
    
    const filter = {};
    if (role) filter.role = role;
    if (isApproved !== undefined) filter.isApproved = isApproved === 'true';
    if (isActive !== undefined) filter.isActive = isActive === 'true';
    
    const users = await User.find(filter).sort('-createdAt');
    
    res.status(200).json({
      success: true,
      count: users.length,
      data: users
    });
  } catch (error) {
    next(error);
  }
};

exports.deactivateUser = async (req, res, next) => {
  try {
    const user = await User.findByIdAndUpdate(
      req.params.userId,
      { isActive: false },
      { new: true }
    );
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    res.status(200).json({
      success: true,
      message: 'User deactivated successfully',
      data: user
    });
  } catch (error) {
    next(error);
  }
};

exports.activateUser = async (req, res, next) => {
  try {
    const user = await User.findByIdAndUpdate(
      req.params.userId,
      { isActive: true },
      { new: true }
    );
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    res.status(200).json({
      success: true,
      message: 'User activated successfully',
      data: user
    });
  } catch (error) {
    next(error);
  }
};

exports.createAdmin = async (req, res, next) => {
  try {
    const { email, password, name, permissions } = req.body;
    
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(400).json({
        success: false,
        message: 'Email already registered'
      });
    }
    
    const user = await User.create({
      email,
      password,
      name,
      role: 'admin',
      isApproved: true
    });
    
    const admin = await Admin.create({
      user: user._id,
      permissions: permissions || ['approve_users', 'view_analytics']
    });
    
    res.status(201).json({
      success: true,
      data: { user, admin }
    });
  } catch (error) {
    next(error);
  }
};

exports.getAdmins = async (req, res, next) => {
  try {
    const admins = await Admin.find()
      .populate('user')
      .sort('-createdAt');
    
    res.status(200).json({
      success: true,
      count: admins.length,
      data: admins
    });
  } catch (error) {
    next(error);
  }
};

exports.deleteAdmin = async (req, res, next) => {
  try {
    const admin = await Admin.findById(req.params.adminId);
    
    if (!admin) {
      return res.status(404).json({
        success: false,
        message: 'Admin not found'
      });
    }
    
    if (admin.isSuperAdmin) {
      return res.status(403).json({
        success: false,
        message: 'Cannot delete super admin'
      });
    }
    
    await User.findByIdAndDelete(admin.user);
    await Admin.findByIdAndDelete(req.params.adminId);
    
    res.status(200).json({
      success: true,
      message: 'Admin deleted successfully'
    });
  } catch (error) {
    next(error);
  }
};

exports.approveCollaboration = async (req, res, next) => {
  try {
    const collaboration = await Collaboration.findByIdAndUpdate(
      req.params.collaborationId,
      {
        status: 'approved',
        approvedBy: req.user._id,
        approvedAt: new Date()
      },
      { new: true }
    );
    
    if (!collaboration) {
      return res.status(404).json({
        success: false,
        message: 'Collaboration not found'
      });
    }
    
    res.status(200).json({
      success: true,
      message: 'Collaboration approved successfully',
      data: collaboration
    });
  } catch (error) {
    next(error);
  }
};

exports.rejectCollaboration = async (req, res, next) => {
  try {
    const { reason } = req.body;
    
    const collaboration = await Collaboration.findByIdAndUpdate(
      req.params.collaborationId,
      {
        status: 'rejected',
        rejectedReason: reason
      },
      { new: true }
    );
    
    if (!collaboration) {
      return res.status(404).json({
        success: false,
        message: 'Collaboration not found'
      });
    }
    
    res.status(200).json({
      success: true,
      message: 'Collaboration rejected successfully',
      data: collaboration
    });
  } catch (error) {
    next(error);
  }
};

exports.getPendingCollaborations = async (req, res, next) => {
  try {
    const collaborations = await Collaboration.find({ status: 'pending' })
      .populate({
        path: 'doctor',
        populate: { path: 'user' }
      })
      .populate({
        path: 'laboratory',
        populate: { path: 'user' }
      })
      .sort('-createdAt');
    
    res.status(200).json({
      success: true,
      count: collaborations.length,
      data: collaborations
    });
  } catch (error) {
    next(error);
  }
};

exports.getStatistics = async (req, res, next) => {
  try {
    const { startDate, endDate } = req.query;
    
    const dateFilter = {};
    if (startDate && endDate) {
      dateFilter.createdAt = {
        $gte: new Date(startDate),
        $lte: new Date(endDate)
      };
    }
    
    // User statistics by role
    const usersByRole = await User.aggregate([
      { $match: dateFilter },
      {
        $group: {
          _id: '$role',
          count: { $sum: 1 }
        }
      }
    ]);
    
    // Prescription statistics
    const prescriptionStats = await Prescription.aggregate([
      { $match: dateFilter },
      {
        $group: {
          _id: '$status',
          count: { $sum: 1 }
        }
      }
    ]);
    
    // Top doctors by prescriptions
    const topDoctors = await Prescription.aggregate([
      { $match: dateFilter },
      {
        $group: {
          _id: '$doctor',
          prescriptionCount: { $sum: 1 }
        }
      },
      { $sort: { prescriptionCount: -1 } },
      { $limit: 10 },
      {
        $lookup: {
          from: 'doctors',
          localField: '_id',
          foreignField: '_id',
          as: 'doctor'
        }
      },
      {
        $lookup: {
          from: 'users',
          localField: 'doctor.user',
          foreignField: '_id',
          as: 'user'
        }
      }
    ]);
    
    // Top labs by reports
    const topLabs = await LabReport.aggregate([
      { $match: dateFilter },
      {
        $group: {
          _id: '$laboratory',
          reportCount: { $sum: 1 }
        }
      },
      { $sort: { reportCount: -1 } },
      { $limit: 10 },
      {
        $lookup: {
          from: 'laboratories',
          localField: '_id',
          foreignField: '_id',
          as: 'laboratory'
        }
      },
      {
        $lookup: {
          from: 'users',
          localField: 'laboratory.user',
          foreignField: '_id',
          as: 'user'
        }
      }
    ]);
    
    res.status(200).json({
      success: true,
      data: {
        usersByRole,
        prescriptionStats,
        topDoctors,
        topLabs
      }
    });
  } catch (error) {
    next(error);
  }
};
```

### File: backend/src/controllers/paymentController.js
```javascript
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);
const Subscription = require('../models/Subscription');
const User = require('../models/User');

const PLANS = {
  basic: {
    name: 'Basic Plan',
    amount: 200000, // ₹2000 in paise
    prescriptionLimit: 100,
    interval: 'month'
  },
  premium: {
    name: 'Premium Plan',
    amount: 500000, // ₹5000 in paise
    prescriptionLimit: null, // Unlimited
    interval: 'month'
  }
};

exports.createCheckoutSession = async (req, res, next) => {
  try {
    const { plan } = req.body;
    
    if (!PLANS[plan]) {
      return res.status(400).json({
        success: false,
        message: 'Invalid plan selected'
      });
    }
    
    const planDetails = PLANS[plan];
    
    // Create Stripe customer
    let customer;
    const user = await User.findById(req.user._id);
    
    if (user.stripeCustomerId) {
      customer = await stripe.customers.retrieve(user.stripeCustomerId);
    } else {
      customer = await stripe.customers.create({
        email: user.email,
        name: user.name,
        metadata: {
          userId: user._id.toString()
        }
      });
      
      user.stripeCustomerId = customer.id;
      await user.save();
    }
    
    // Create checkout session
    const session = await stripe.checkout.sessions.create({
      customer: customer.id,
      payment_method_types: ['card'],
      line_items: [
        {
          price_data: {
            currency: 'inr',
            product_data: {
              name: planDetails.name,
              description: planDetails.prescriptionLimit 
                ? `Up to ${planDetails.prescriptionLimit} prescriptions per month`
                : 'Unlimited prescriptions'
            },
            unit_amount: planDetails.amount,
            recurring: {
              interval: planDetails.interval
            }
          },
          quantity: 1
        }
      ],
      mode: 'subscription',
      success_url: `${process.env.FRONTEND_URL}/payment/success?session_id={CHECKOUT_SESSION_ID}`,
      cancel_url: `${process.env.FRONTEND_URL}/payment/cancel`,
      metadata: {
        userId: req.user._id.toString(),
        plan: plan
      }
    });
    
    res.status(200).json({
      success: true,
      sessionId: session.id,
      url: session.url
    });
  } catch (error) {
    next(error);
  }
};

exports.handleWebhook = async (req, res, next) => {
  const sig = req.headers['stripe-signature'];
  
  let event;
  
  try {
    event = stripe.webhooks.constructEvent(
      req.body,
      sig,
      process.env.STRIPE_WEBHOOK_SECRET
    );
  } catch (err) {
    return res.status(400).send(`Webhook Error: ${err.message}`);
  }
  
  try {
    switch (event.type) {
      case 'checkout.session.completed':
        await handleCheckoutComplete(event.data.object);
        break;
      
      case 'invoice.payment_succeeded':
        await handlePaymentSuccess(event.data.object);
        break;
      
      case 'invoice.payment_failed':
        await handlePaymentFailed(event.data.object);
        break;
      
      case 'customer.subscription.deleted':
        await handleSubscriptionCancelled(event.data.object);
        break;
      
      default:
        console.log(`Unhandled event type: ${event.type}`);
    }
    
    res.json({ received: true });
  } catch (error) {
    console.error('Webhook handler error:', error);
    res.status(500).json({ error: 'Webhook handler failed' });
  }
};

const handleCheckoutComplete = async (session) => {
  const userId = session.metadata.userId;
  const plan = session.metadata.plan;
  const planDetails = PLANS[plan];
  
  const endDate = new Date();
  endDate.setMonth(endDate.getMonth() + 1);
  
  const subscription = await Subscription.create({
    user: userId,
    plan: plan,
    amount: planDetails.amount / 100, // Convert from paise to rupees
    prescriptionLimit: planDetails.prescriptionLimit,
    endDate: endDate,
    stripeSubscriptionId: session.subscription,
    stripeCustomerId: session.customer,
    status: 'active'
  });
  
  await User.findByIdAndUpdate(userId, {
    subscription: subscription._id
  });
};

const handlePaymentSuccess = async (invoice) => {
  const subscriptionId = invoice.subscription;
  
  await Subscription.findOneAndUpdate(
    { stripeSubscriptionId: subscriptionId },
    {
      status: 'active',
      prescriptionCount: 0, // Reset count on renewal
      $inc: { 'endDate': 30 * 24 * 60 * 60 * 1000 } // Add 30 days
    }
  );
};

const handlePaymentFailed = async (invoice) => {
  const subscriptionId = invoice.subscription;
  
  await Subscription.findOneAndUpdate(
    { stripeSubscriptionId: subscriptionId },
    { status: 'cancelled' }
  );
};

const handleSubscriptionCancelled = async (subscription) => {
  await Subscription.findOneAndUpdate(
    { stripeSubscriptionId: subscription.id },
    {
      status: 'cancelled',
      autoRenew: false
    }
  );
};

exports.getSubscription = async (req, res, next) => {
  try {
    const subscription = await Subscription.findOne({
      user: req.user._id,
      status: 'active',
      endDate: { $gte: new Date() }
    });
    
    res.status(200).json({
      success: true,
      data: subscription
    });
  } catch (error) {
    next(error);
  }
};

exports.cancelSubscription = async (req, res, next) => {
  try {
    const subscription = await Subscription.findOne({
      user: req.user._id,
      status: 'active'
    });
    
    if (!subscription) {
      return res.status(404).json({
        success: false,
        message: 'No active subscription found'
      });
    }
    
    // Cancel on Stripe
    await stripe.subscriptions.update(subscription.stripeSubscriptionId, {
      cancel_at_period_end: true
    });
    
    subscription.autoRenew = false;
    await subscription.save();
    
    res.status(200).json({
      success: true,
      message: 'Subscription will be cancelled at the end of billing period',
      data: subscription
    });
  } catch (error) {
    next(error);
  }
};

exports.getPlans = async (req, res, next) => {
  try {
    res.status(200).json({
      success: true,
      data: Object.keys(PLANS).map(key => ({
        id: key,
        ...PLANS[key],
        amountInRupees: PLANS[key].amount / 100
      }))
    });
  } catch (error) {
    next(error);
  }
};
```

## Routes

### File: backend/src/routes/auth.js
```javascript
const express = require('express');
const router = express.Router();
const {
  register,
  login,
  googleAuth,
  facebookAuth,
  getMe
} = require('../controllers/authController');
const { protect } = require('../middleware/auth');

router.post('/register', register);
router.post('/login', login);
router.post('/google', googleAuth);
router.post('/facebook', facebookAuth);
router.get('/me', protect, getMe);

module.exports = router;
```

### File: backend/src/routes/doctor.js
```javascript
const express = require('express');
const router = express.Router();
const {
  getDashboard,
  updateProfile,
  uploadSignature,
  getPatients,
  getPatientDetails,
  createNote,
  getNotes,
  updateNote,
  deleteNote,
  getRareCases,
  createRareCase
} = require('../controllers/doctorController');
const { protect, checkApproval, checkSubscription } = require('../middleware/auth');
const { authorize } = require('../middleware/roleCheck');
const { upload } = require('../middleware/uploadHandler');

// All routes require authentication and doctor role
router.use(protect, authorize('doctor'), checkApproval);

router.get('/dashboard', getDashboard);
router.put('/profile', updateProfile);
router.post('/signature', upload.single('signature'), uploadSignature);
router.get('/patients', getPatients);
router.get('/patients/:id', getPatientDetails);

// Notes
router.route('/notes')
  .get(getNotes)
  .post(createNote);

router.route('/notes/:id')
  .put(updateNote)
  .delete(deleteNote);

// Rare Cases
router.route('/rare-cases')
  .get(getRareCases)
  .post(createRareCase);

module.exports = router;
```

### File: backend/src/routes/lab.js
```javascript
const express = require('express');
const router = express.Router();
const {
  getDashboard,
  updateProfile,
  uploadReport,
  getReports,
  requestCollaboration,
  getCollaborations,
  getPatients
} = require('../controllers/labController');
const { protect, checkApproval, checkSubscription } = require('../middleware/auth');
const { authorize } = require('../middleware/roleCheck');
const { upload } = require('../middleware/uploadHandler');

// All routes require authentication and laboratory role
router.use(protect, authorize('laboratory'), checkApproval);

router.get('/dashboard', getDashboard);
router.put('/profile', updateProfile);
router.post('/reports', checkSubscription, upload.array('files', 10), uploadReport);
router.get('/reports', getReports);
router.post('/collaborations', requestCollaboration);
router.get('/collaborations', getCollaborations);
router.get('/patients', getPatients);

module.exports = router;
```

### File: backend/src/routes/admin.js
```javascript
const express = require('express');
const router = express.Router();
const {
  getDashboard,
  getPendingApprovals,
  approveUser,
  rejectUser,
  getAllUsers,
  deactivateUser,
  activateUser,
  createAdmin,
  getAdmins,
  deleteAdmin,
  approveCollaboration,
  rejectCollaboration,
  getPendingCollaborations,
  getStatistics
} = require('../controllers/adminController');
const { protect, checkApproval } = require('../middleware/auth');
const { authorize } = require('../middleware/roleCheck');

// All routes require authentication and admin role
router.use(protect, authorize('admin'), checkApproval);

router.get('/dashboard', getDashboard);
router.get('/statistics', getStatistics);

// User management
router.get('/users', getAllUsers);
router.get('/pending-approvals', getPendingApprovals);
router.put('/users/:userId/approve', approveUser);
router.put('/users/:userId/reject', rejectUser);
router.put('/users/:userId/deactivate', deactivateUser);
router.put('/users/:userId/activate', activateUser);

// Admin management
router.route('/admins')
  .get(getAdmins)
  .post(createAdmin);

router.delete('/admins/:adminId', deleteAdmin);

// Collaboration management
router.get('/collaborations/pending', getPendingCollaborations);
router.put('/collaborations/:collaborationId/approve', approveCollaboration);
router.put('/collaborations/:collaborationId/reject', rejectCollaboration);

module.exports = router;
```

### File: backend/src/routes/prescription.js
```javascript
const express = require('express');
const router = express.Router();
const {
  createPrescription,
  updatePrescription,
  completePrescription,
  sendPrescription,
  getPrescriptions,
  getPrescriptionById,
  deletePrescription,
  createPatient
} = require('../controllers/prescriptionController');
const { protect, checkApproval, checkSubscription } = require('../middleware/auth');
const { authorize } = require('../middleware/roleCheck');

// All routes require authentication and doctor role
router.use(protect, authorize('doctor'), checkApproval);

router.route('/')
  .get(getPrescriptions)
  .post(checkSubscription, createPrescription);

router.route('/:id')
  .get(getPrescriptionById)
  .put(updatePrescription)
  .delete(deletePrescription);

router.put('/:id/complete', completePrescription);
router.post('/:id/send', sendPrescription);

// Patient routes
router.post('/patients', createPatient);

module.exports = router;
```

### File: backend/src/routes/payment.js
```javascript
const express = require('express');
const router = express.Router();
const {
  createCheckoutSession,
  handleWebhook,
  getSubscription,
  cancelSubscription,
  getPlans
} = require('../controllers/paymentController');
const { protect } = require('../middleware/auth');

router.get('/plans', getPlans);
router.post('/create-checkout-session', protect, createCheckoutSession);
router.post('/webhook', express.raw({ type: 'application/json' }), handleWebhook);
router.get('/subscription', protect, getSubscription);
router.post('/cancel-subscription', protect, cancelSubscription);

module.exports = router;
```

## Frontend Implementation

### File: frontend/package.json
```json
{
  "name": "digital-prescription-frontend",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "@ant-design/icons": "^5.2.6",
    "@stripe/react-stripe-js": "^2.4.0",
    "@stripe/stripe-js": "^2.2.0",
    "antd": "^5.11.5",
    "axios": "^1.6.2",
    "fabric": "^5.3.0",
    "html2canvas": "^1.4.1",
    "jspdf": "^2.5.1",
    "moment": "^2.29.4",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-facebook-login": "^4.1.1",
    "react-google-login": "^5.2.2",
    "react-router-dom": "^6.20.1",
    "react-scripts": "5.0.1",
    "recharts": "^2.10.3"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": [
      "react-app"
    ]
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  }
}
```

### File: frontend/src/index.jsx
```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import { ConfigProvider } from 'antd';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <ConfigProvider
      theme={{
        token: {
          colorPrimary: '#1890ff',
          borderRadius: 6
        }
      }}
    >
      <App />
    </ConfigProvider>
  </React.StrictMode>
);
```

### File: frontend/src/App.jsx
```javascript
import React from 'react';
import { BrowserRouter as Router, Routes, Route, Navigate } from 'react-router-dom';
import { AuthProvider } from './context/AuthContext';
import Login from './components/Auth/Login';
import ProtectedRoute from './components/Auth/ProtectedRoute';
import DoctorDashboard from './components/Doctor/Dashboard';
import PrescriptionCanvas from './components/Doctor/PrescriptionCanvas';
import PrescriptionHistory from './components/Doctor/PrescriptionHistory';
import RareCases from './components/Doctor/RareCases';
import Notes from './components/Doctor/Notes';
import PatientView from './components/Doctor/PatientView';
import ProfileEdit from './components/Doctor/ProfileEdit';
import LabDashboard from './components/Laboratory/LabDashboard';
import UploadReports from './components/Laboratory/UploadReports';
import Collaborations from './components/Laboratory/Collaborations';
import AdminDashboard from './components/Admin/AdminDashboard';
import UserApproval from './components/Admin/UserApproval';
import Statistics from './components/Admin/Statistics';
import AdminManagement from './components/Admin/AdminManagement';
import SubscriptionPortal from './components/Payment/SubscriptionPortal';
import './App.css';

function App() {
  return (
    <Router>
      <AuthProvider>
        <Routes>
          <Route path="/login" element={<Login />} />
          
          {/* Doctor Routes */}
          <Route
            path="/doctor/dashboard"
            element={
              <ProtectedRoute role="doctor">
                <DoctorDashboard />
              </ProtectedRoute>
            }
          />
          <Route
            path="/doctor/prescription/new"
            element={
              <ProtectedRoute role="doctor">
                <PrescriptionCanvas />
              </ProtectedRoute>
            }
          />
          <Route
            path="/doctor/prescription/edit/:id"
            element={
              <ProtectedRoute role="doctor">
                <PrescriptionCanvas />
              </ProtectedRoute>
            }
          />
          <Route
            path="/doctor/prescriptions"
            element={
              <ProtectedRoute role="doctor">
                <PrescriptionHistory />
              </ProtectedRoute>
            }
          />
          <Route
            path="/doctor/rare-cases"
            element={
              <ProtectedRoute role="doctor">
                <RareCases />
              </ProtectedRoute>
            }
          />
          <Route
            path="/doctor/notes"
            element={
              <ProtectedRoute role="doctor">
                <Notes />
              </ProtectedRoute>
            }
          />
          <Route
            path="/doctor/patients"
            element={
              <ProtectedRoute role="doctor">
                <PatientView />
              </ProtectedRoute>
            }
          />
          <Route
            path="/doctor/profile"
            element={
              <ProtectedRoute role="doctor">
                <ProfileEdit />
              </ProtectedRoute>
            }
          />
          
          {/* Laboratory Routes */}
          <Route
            path="/lab/dashboard"
            element={
              <ProtectedRoute role="laboratory">
                <LabDashboard />
              </ProtectedRoute>
            }
          />
          <Route
            path="/lab/upload"
            element={
              <ProtectedRoute role="laboratory">
                <UploadReports />
              </ProtectedRoute>
            }
          />
          <Route
            path="/lab/collaborations"
            element={
              <ProtectedRoute role="laboratory">
                <Collaborations />
              </ProtectedRoute>
            }
          />
          
          {/* Admin Routes */}
          <Route
            path="/admin/dashboard"
            element={
              <ProtectedRoute role="admin">
                <AdminDashboard />
              </ProtectedRoute>
            }
          />
          <Route
            path="/admin/approvals"
            element={
              <ProtectedRoute role="admin">
                <UserApproval />
              </ProtectedRoute>
            }
          />
          <Route
            path="/admin/statistics"
            element={
              <ProtectedRoute role="admin">
                <Statistics />
              </ProtectedRoute>
            }
          />
          <Route
            path="/admin/admins"
            element={
              <ProtectedRoute role="admin">
                <AdminManagement />
              </ProtectedRoute>
            }
          />
          
          {/* Payment Routes */}
          <Route
            path="/subscription"
            element={
              <ProtectedRoute>
                <SubscriptionPortal />
              </ProtectedRoute>
            }
          />
          
          <Route path="/" element={<Navigate to="/login" />} />
        </Routes>
      </AuthProvider>
    </Router>
  );
}

export default App;
```

### File: frontend/src/App.css
```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: #f0f2f5;
}

.app-container {
  min-height: 100vh;
  display: flex;
}

.main-content {
  flex: 1;
  padding: 24px;
  margin-left: 200px;
  transition: margin-left 0.2s;
}

.main-content.collapsed {
  margin-left: 80px;
}

@media (max-width: 768px) {
  .main-content {
    margin-left: 0;
    padding: 16px;
  }
}
```

### File: frontend/src/context/AuthContext.jsx
```javascript
import React, { createContext, useState, useEffect, useContext } from 'react';
import { authService } from '../services/authService';

const AuthContext = createContext();

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
};

export const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);
  const [profile, setProfile] = useState(null);
  const [loading, setLoading] = useState(true);
  const [isAuthenticated, setIsAuthenticated] = useState(false);

  useEffect(() => {
    checkAuth();
  }, []);

  const checkAuth = async () => {
    try {
      const token = localStorage.getItem('token');
      if (token) {
        const response = await authService.getMe();
        setUser(response.user);
        setProfile(response.profile);
        setIsAuthenticated(true);
      }
    } catch (error) {
      console.error('Auth check failed:', error);
      logout();
    } finally {
      setLoading(false);
    }
  };

  const login = async (credentials) => {
    try {
      const response = await authService.login(credentials);
      localStorage.setItem('token', response.token);
      setUser(response.user);
      setIsAuthenticated(true);
      await checkAuth();
      return response;
    } catch (error) {
      throw error;
    }
  };

  const register = async (data) => {
    try {
      const response = await authService.register(data);
      localStorage.setItem('token', response.token);
      setUser(response.user);
      setIsAuthenticated(true);
      return response;
    } catch (error) {
      throw error;
    }
  };

  const googleLogin = async (data) => {
    try {
      const response = await authService.googleAuth(data);
      localStorage.setItem('token', response.token);
      setUser(response.user);
      setIsAuthenticated(true);
      await checkAuth();
      return response;
    } catch (error) {
      throw error;
    }
  };

  const facebookLogin = async (data) => {
    try {
      const response = await authService.facebookAuth(data);
      localStorage.setItem('token', response.token);
      setUser(response.user);
      setIsAuthenticated(true);
      await checkAuth();
      return response;
    } catch (error) {
      throw error;
    }
  };

  const logout = () => {
    localStorage.removeItem('token');
    setUser(null);
    setProfile(null);
    setIsAuthenticated(false);
  };

  const updateProfile = (updatedProfile) => {
    setProfile(updatedProfile);
  };

  const value = {
    user,
    profile,
    loading,
    isAuthenticated,
    login,
    register,
    googleLogin,
    facebookLogin,
    logout,
    updateProfile
  };

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
};
```

### File: frontend/src/services/api.js
```javascript
import axios from 'axios';

const API_URL = process.env.REACT_APP_API_URL || 'http://localhost:5000/api';

const api = axios.create({
  baseURL: API_URL,
  headers: {
    'Content-Type': 'application/json'
  }
});

// Request interceptor
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Response interceptor
api.interceptors.response.use(
  (response) => {
    return response.data;
  },
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error.response?.data || error.message);
  }
);

export default api;
```

### File: frontend/src/services/authService.js
```javascript
import api from './api';

export const authService = {
  login: async (credentials) => {
    return await api.post('/auth/login', credentials);
  },

  register: async (data) => {
    return await api.post('/auth/register', data);
  },

  googleAuth: async (data) => {
    return await api.post('/auth/google', data);
  },

  facebookAuth: async (data) => {
    return await api.post('/auth/facebook', data);
  },

  getMe: async () => {
    return await api.get('/auth/me');
  }
};
```

### File: frontend/src/services/prescriptionService.js
```javascript
import api from './api';

export const prescriptionService = {
  createPrescription: async (data) => {
    return await api.post('/prescription', data);
  },

  updatePrescription: async (id, data) => {
    return await api.put(`/prescription/${id}`, data);
  },

  completePrescription: async (id, canvasImages) => {
    return await api.put(`/prescription/${id}/complete`, { canvasImages });
  },

  sendPrescription: async (id) => {
    return await api.post(`/prescription/${id}/send`);
  },

  getPrescriptions: async () => {
    return await api.get('/prescription');
  },

  getPrescriptionById: async (id) => {
    return await api.get(`/prescription/${id}`);
  },

  deletePrescription: async (id) => {
    return await api.delete(`/prescription/${id}`);
  },

  createPatient: async (data) => {
    return await api.post('/prescription/patients', data);
  }
};
```

### File: frontend/src/components/Auth/Login.jsx
```javascript
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { Form, Input, Button, Card, Tabs, message, Space, Divider } from 'antd';
import {
  UserOutlined,
  LockOutlined,
  MailOutlined,
  PhoneOutlined,
  GoogleOutlined,
  FacebookOutlined
} from '@ant-design/icons';
import { useAuth } from '../../context/AuthContext';
import GoogleLogin from 'react-google-login';
import FacebookLogin from 'react-facebook-login/dist/facebook-login-render-props';
import './Login.css';

const Login = () => {
  const [loading, setLoading] = useState(false);
  const [activeTab, setActiveTab] = useState('login');
  const { login, register, googleLogin, facebookLogin, isAuthenticated, user } = useAuth();
  const navigate = useNavigate();

  React.useEffect(() => {
    if (isAuthenticated && user) {
      redirectToDashboard(user.role);
    }
  }, [isAuthenticated, user]);

  const redirectToDashboard = (role) => {
    switch (role) {
      case 'doctor':
        navigate('/doctor/dashboard');
        break;
      case 'laboratory':
        navigate('/lab/dashboard');
        break;
      case 'admin':
        navigate('/admin/dashboard');
        break;
      default:
        navigate('/');
    }
  };

  const onLoginFinish = async (values) => {
    setLoading(true);
    try {
      const response = await login(values);
      message.success('Login successful!');
      
      if (!response.user.isApproved && response.user.role !== 'admin') {
        message.warning('Your account is pending approval from admin');
      } else {
        redirectToDashboard(response.user.role);
      }
    } catch (error) {
      message.error(error.message || 'Login failed');
    } finally {
      setLoading(false);
    }
  };

  const onRegisterFinish = async (values) => {
    setLoading(true);
    try {
      const response = await register(values);
      message.success('Registration successful! Please wait for admin approval.');
      setActiveTab('login');
    } catch (error) {
      message.error(error.message || 'Registration failed');
    } finally {
      setLoading(false);
    }
  };

  const onGoogleSuccess = async (response) => {
    try {
      const data = {
        googleId: response.googleId,
        email: response.profileObj.email,
        name: response.profileObj.name,
        avatar: response.profileObj.imageUrl,
        role: 'doctor' // Default role, can be changed
      };
      
      const result = await googleLogin(data);
      message.success('Google login successful!');
      redirectToDashboard(result.user.role);
    } catch (error) {
      message.error('Google login failed');
    }
  };

  const onGoogleFailure = (error) => {
    console.error('Google login error:', error);
    message.error('Google login failed');
  };

  const onFacebookResponse = async (response) => {
    if (response.accessToken) {
      try {
        const data = {
          facebookId: response.id,
          email: response.email,
          name: response.name,
          avatar: response.picture?.data?.url,
          role: 'doctor' // Default role
        };
        
        const result = await facebookLogin(data);
        message.success('Facebook login successful!');
        redirectToDashboard(result.user.role);
      } catch (error) {
        message.error('Facebook login failed');
      }
    }
  };

  const loginForm = (
    <Form
      name="login"
      onFinish={onLoginFinish}
      autoComplete="off"
      layout="vertical"
    >
      <Form.Item
        name="email"
        rules={[
          { required: true, message: 'Please input your email!' },
          { type: 'email', message: 'Please enter a valid email!' }
        ]}
      >
        <Input
          prefix={<MailOutlined />}
          placeholder="Email"
          size="large"
        />
      </Form.Item>

      <Form.Item
        name="password"
        rules={[{ required: true, message: 'Please input your password!' }]}
      >
        <Input.Password
          prefix={<LockOutlined />}
          placeholder="Password"
          size="large"
        />
      </Form.Item>

      <Form.Item>
        <Button type="primary" htmlType="submit" loading={loading} block size="large">
          Log in
        </Button>
      </Form.Item>

      <Divider>Or login with</Divider>

      <Space direction="vertical" style={{ width: '100%' }} size="middle">
        <GoogleLogin
          clientId={process.env.REACT_APP_GOOGLE_CLIENT_ID}
          render={(renderProps) => (
            <Button
              icon={<GoogleOutlined />}
              onClick={renderProps.onClick}
              disabled={renderProps.disabled}
              block
              size="large"
            >
              Continue with Google
            </Button>
          )}
          onSuccess={onGoogleSuccess}
          onFailure={onGoogleFailure}
          cookiePolicy={'single_host_origin'}
        />

        <FacebookLogin
          appId={process.env.REACT_APP_FACEBOOK_APP_ID}
          callback={onFacebookResponse}
          fields="name,email,picture"
          render={(renderProps) => (
            <Button
              icon={<FacebookOutlined />}
              onClick={renderProps.onClick}
              block
              size="large"
            >
              Continue with Facebook
            </Button>
          )}
        />
      </Space>
    </Form>
  );

  const registerForm = (
    <Form
      name="register"
      onFinish={onRegisterFinish}
      autoComplete="off"
      layout="vertical"
    >
      <Form.Item
        name="name"
        rules={[{ required: true, message: 'Please input your name!' }]}
      >
        <Input
          prefix={<UserOutlined />}
          placeholder="Full Name"
          size="large"
        />
      </Form.Item>

      <Form.Item
        name="email"
        rules={[
          { required: true, message: 'Please input your email!' },
          { type: 'email', message: 'Please enter a valid email!' }
        ]}
      >
        <Input
          prefix={<MailOutlined />}
          placeholder="Email"
          size="large"
        />
      </Form.Item>

      <Form.Item
        name="password"
        rules={[
          { required: true, message: 'Please input your password!' },
          { min: 6, message: 'Password must be at least 6 characters!' }
        ]}
      >
        <Input.Password
          prefix={<LockOutlined />}
          placeholder="Password"
          size="large"
        />
      </Form.Item>

      <Form.Item
        name="role"
        rules={[{ required: true, message: 'Please select your role!' }]}
      >
        <Input.Group compact>
          <Button.Group style={{ width: '100%' }}>
            <Button style={{ width: '50%' }} size="large">Doctor</Button>
            <Button style={{ width: '50%' }} size="large">Laboratory</Button>
          </Button.Group>
        </Input.Group>
      </Form.Item>

      <Form.Item
        name="registrationNumber"
        rules={[{ required: true, message: 'Please input your registration number!' }]}
      >
        <Input
          placeholder="Registration Number"
          size="large"
        />
      </Form.Item>

      <Form.Item
        name="contactNumber"
        rules={[{ required: true, message: 'Please input your contact number!' }]}
      >
        <Input
          prefix={<PhoneOutlined />}
          placeholder="Contact Number"
          size="large"
        />
      </Form.Item>

      <Form.Item>
        <Button type="primary" htmlType="submit" loading={loading} block size="large">
          Register
        </Button>
      </Form.Item>
    </Form>
  );

  return (
    <div className="login-container">
      <Card
        className="login-card"
        title={
          <div className="login-header">
            <h2>Digital Prescription</h2>
            <p>Healthcare Management System</p>
          </div>
        }
      >
        <Tabs
          activeKey={activeTab}
          onChange={setActiveTab}
          centered
          items={[
            {
              key: 'login',
              label: 'Login',
              children: loginForm
            },
            {
              key: 'register',
              label: 'Register',
              children: registerForm
            }
          ]}
        />
      </Card>
    </div>
  );
};

export default Login;
```

### File: frontend/src/components/Auth/Login.css
```css
.login-container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 20px;
}

.login-card {
  width: 100%;
  max-width: 450px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
  border-radius: 12px;
}

.login-header {
  text-align: center;
  margin-bottom: 20px;
}

.login-header h2 {
  margin: 0;
  color: #1890ff;
  font-size: 28px;
  font-weight: 700;
}

.
