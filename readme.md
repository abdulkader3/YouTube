# YouTube Clone Backend API Documentation

This documentation is designed to help frontend developers understand and interact with the YouTube Clone Backend API. It's written with beginners in mind, so don't worry if you're just starting out!

## 🌟 Getting Started

### Base URL
```
http://localhost:9000/api/v1
```

### Project Setup
1. The backend uses Node.js with Express
2. Database: MongoDB (Database name: YouTube)
3. File Storage: Cloudinary for avatars, cover images, and video files
4. Authentication: JWT (JSON Web Tokens) with refresh token mechanism

## 📝 API Endpoints

### User Management

#### 1. Register a New User
- **Endpoint:** `POST /users/register`
- **What it does:** Creates a new user account
- **What you need to send:** Form Data with:
  ```json
  {
    "username": "johndoe",
    "email": "john@example.com",
    "password": "securepassword",
    "fullName": "John Doe",
    "avatar": "Image File (optional)",
    "coverImage": "Image File (optional)"
  }
  ```
- **What you'll get back:**
  ```json
  {
    "success": true,
    "message": "User registered successfully",
    "data": {
      "user": {
        "_id": "user_id",
        "username": "johndoe",
        "email": "john@example.com",
        "fullName": "John Doe",
        "avatar": "avatar_url",
        "coverImage": "cover_image_url"
      }
    }
  }
  ```

#### 2. Login User
- **Endpoint:** `POST /users/login`
- **What it does:** Logs in a user and provides an access token
- **What you need to send:**
  ```json
  {
    "email": "john@example.com",
    "password": "securepassword"
  }
  ```
- **What you'll get back:**
  ```json
  {
    "success": true,
    "message": "Logged in successfully",
    "data": {
      "accessToken": "your_access_token",
      "user": {
        "_id": "user_id",
        "username": "johndoe",
        "email": "john@example.com"
      }
    }
  }
  ```

#### 3. Get Current User Data
- **Endpoint:** `POST /users/current-user-data`
- **What it does:** Returns the current user's profile information
- **Headers Required:** `Authorization: Bearer your_access_token`
- **What you'll get back:**
  ```json
  {
    "success": true,
    "data": {
      "user": {
        "_id": "user_id",
        "username": "johndoe",
        "email": "john@example.com",
        "fullName": "John Doe",
        "avatar": "avatar_url",
        "coverImage": "cover_image_url"
      }
    }
  }
  ```

#### 4. Change Password
- **Endpoint:** `POST /users/change-password`
- **Headers Required:** `Authorization: Bearer your_access_token`
- **What you need to send:**
  ```json
  {
    "oldPassword": "currentPassword",
    "newPassword": "newSecurePassword"
  }
  ```

#### 5. Change Account Details
- **Endpoint:** `POST /users/change-details`
- **Headers Required:** `Authorization: Bearer your_access_token`
- **What you need to send:**
  ```json
  {
    "fullName": "New Name",
    "email": "newemail@example.com"
  }
  ```

#### 6. Change Avatar
- **Endpoint:** `POST /users/change-avatar`
- **Headers Required:** `Authorization: Bearer your_access_token`
- **What you need to send:** Form Data with:
  - avatar: Image File

#### 7. Change Cover Image
- **Endpoint:** `POST /users/change-coverImage`
- **Headers Required:** `Authorization: Bearer your_access_token`
- **What you need to send:** Form Data with:
  - coverImage: Image File

#### 8. Refresh Access Token
- **Endpoint:** `POST /users/refreshed-token`
- **Headers Required:** `Authorization: Bearer your_access_token`
- **What it does:** Issues a new access token using the refresh token

#### 9. Logout
- **Endpoint:** `POST /users/logout`
- **Headers Required:** `Authorization: Bearer your_access_token`
- **What it does:** Invalidates the current access token

### Video Management

#### 1. Upload a Video
- **Endpoint:** `POST /videos/upload`
- **Important:** This needs to be a multipart/form-data request
- **What you need to send:**
  - videoFile: Your video file
  - thumbnail: Thumbnail image for the video
  - title: Video title
  - description: Video description
- **Headers Required:**
  ```
  Authorization: Bearer your_access_token
  ```

#### 2. Get All Videos
- **Endpoint:** `GET /videos`
- **What it does:** Returns a list of all videos
- **Optional Query Parameters:**
  - page: Page number (default: 1)
  - limit: Number of videos per page (default: 10)
  - search: Search videos by title

## 🔒 Authentication

For most endpoints (except login and register), you need to include an Authorization header:
```
Authorization: Bearer your_access_token
```

## 🚨 Error Handling

The API returns consistent error responses:
```json
{
  "success": false,
  "message": "Error message here",
  "errors": ["Detailed error information"]
}
```

Common Status Codes:
- 200: Success
- 201: Created successfully
- 400: Bad request (check your data)
- 401: Unauthorized (login required)
- 403: Forbidden (not allowed)
- 404: Not found
- 500: Server error

## 📁 File Upload Guidelines

1. For video uploads:
   - Supported formats: MP4, WebM
   - Maximum file size: 100MB
   - Files are stored on Cloudinary

2. For thumbnails:
   - Supported formats: JPG, PNG
   - Recommended size: 1280x720 pixels
   - Maximum file size: 5MB

## 👀 Quick Tips for Beginners

1. Always handle errors in your frontend code:
   ```javascript
   try {
     const response = await fetch('http://localhost:4000/api/v1/users/login', {
       method: 'POST',
       headers: {
         'Content-Type': 'application/json'
       },
       body: JSON.stringify({
         email: userEmail,
         password: userPassword
       })
     });
     const data = await response.json();
     if (!data.success) {
       throw new Error(data.message);
     }
     // Handle successful response
   } catch (error) {
     // Show error message to user
     console.error('Error:', error.message);
   }
   ```

2. Store the access token safely:
   ```javascript
   // After login success
   localStorage.setItem('accessToken', data.data.accessToken);

   // When making authenticated requests
   const token = localStorage.getItem('accessToken');
   fetch('http://localhost:4000/api/v1/videos', {
     headers: {
       'Authorization': `Bearer ${token}`
     }
   });
   ```

3. For file uploads, use FormData:
   ```javascript
   const formData = new FormData();
   formData.append('videoFile', videoFileInput.files[0]);
   formData.append('thumbnail', thumbnailInput.files[0]);
   formData.append('title', 'My Video Title');
   formData.append('description', 'Video description here');

   fetch('http://localhost:4000/api/v1/videos/upload', {
     method: 'POST',
     headers: {
       'Authorization': `Bearer ${token}`
     },
     body: formData
   });
   ```

## 🤔 Need Help?

If you encounter any issues:
1. Check if you're using the correct endpoint
2. Verify your request data matches the required format
3. Make sure you're including the authorization token for protected routes
4. Check the error message returned by the API


## 🚀 Next Steps

1. Start by implementing user registration and login
2. Once you have authentication working, try the video upload feature
3. Implement the video listing and search functionality
4. Add user profile features
5. You can also contact me. I'm the backend developer who built this project. My Contact 01970713237

Remember: Always test your API calls in a tool like Postman before implementing them in your frontend code!