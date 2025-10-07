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
        
        if (hospital.
