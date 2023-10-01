# node-express-simple

## 1. Create simple node server
```js
require('dotenv').config()
const express = require('express')

const PORT = process.env.PORT || 5000

const app = express()

app.listen(PORT, () => console.log(`Server is running on port ${PORT}`))
```

## 2. Connect Cloud MongoDB
```js
require('dotenv').config()
const mongoose = require('mongoose')

const DB_USERNAME = process.env.DB_USERNAME
const DB_PASSWORD = process.env.DB_PASSWORD

const connectDB = async () => {
  try {
    await mongoose.connect(
      `mongodb+srv://${DB_USERNAME}:${DB_PASSWORD}@cluster0.bgiytvm.mongodb.net/?retryWrites=true&w=majority`,
      {
        useNewUrlParser: true,
        useUnifiedTopology: true,
      }
    )

    console.log('MongoDB connected')
  } catch (error) {
    console.error(error.message)
    process.exit(1)
  }
}

connectDB()
```

## 3. Create Models
```js
const mongoose = require('mongoose')
const Schema = mongoose.Schema

const UserSchema = new Schema({
  username: {
    type: String,
    trim: true,
    required: [true, 'Name must be required!'],
    unique: true,
  },
  password: {
    type: String,
    required: [true, 'Password must be required!'],
    minLength: [6, 'Password must be at least 6 characters']
  },
  createdAt: {
    type: Date,
    default: Date.now,
  },
})

module.exports = mongoose.model('users', UserSchema)
```

## 4. Middleware VerifyToken
```js
const jwt = require('jsonwebtoken')

const verifyToken = (req, res, next) => {
  const authHeader = req.header('Authorization')
  const token = authHeader && authHeader.split(' ')[1]

  if (!token) {
    return res
      .status(401)
      .json({ success: false, message: 'Access token not found' })
  }

  try {
    const decoded = jwt.verify(token, process.env.ACCESS_TOKEN_SECRET)

    req.userId = decoded.userId
    next()
  } catch (error) {
    console.error(error)
    return res.status(403).json({ success: false, message: 'Invalid token' })
  }
}

module.exports = verifyToken
```
