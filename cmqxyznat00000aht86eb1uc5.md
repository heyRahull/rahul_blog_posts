---
title: "2.0 How to upload file in backend - Multer"
datePublished: 2026-06-28T15:55:04.765Z
cuid: cmqxyznat00000aht86eb1uc5
slug: 2-0-how-to-upload-file-in-backend-multer

---

Handling file uploads—whether they are images, PDFs, videos, or audio—is a foundational requirement for modern backend systems. In production environments, roughly 90% of file-handling logic resides squarely on the backend engineer. The frontend typically just renders a form and passes the raw file chunk or browser link; the true heavy lifting happens on the server.

Standard web frameworks like Express do not come equipped with native file-handling capabilities out of the box. This guide breaks down an industry-standard, production-grade strategy using a **two-step upload mechanism** via **Multer** and **Cloudinary**.

* * *

## 1\. Architectural Strategy: The Two-Step Upload

In heavy production environments, it is rarely advisable to stream large files directly from a user's request straight to a cloud asset manager. If the third-party API hiccups or fails halfway through, the file chunk is lost, leaving no way to re-attempt the process cleanly.

Instead, professional architectures favor a reliable **two-step approach**:

1.  **Local Staging:** The user uploads the file via an HTTP request. The backend intercepts it using a middleware layer (like Multer) and writes it temporarily into a designated directory on the local server disks.
    
2.  **Cloud Sync:** A standalone utility reads the staged file from the local directory and synchronously pushes it up to a specialized third-party cloud service (like Cloudinary or AWS S3). Once verified as successfully uploaded, the server unlinks (deletes) the local temporary file to clean up disk space.
    

* * *

## 2\. Setting Up Cloudinary as a Core Utility (`cloudinary.js`)

Instead of cluttering request controllers with direct upload configurations, file synchronization belongs in a distinct, reusable utility function. This single piece of code can cleanly handle any resource type—be it an image, PDF, or video asset.

### Code Implementation

```javascript
import { v2 as cloudinary } from "cloudinary";
import fs from "fs";

// Configure Cloudinary with secure environment variables
cloudinary.config({ 
  cloud_name: process.env.CLOUDINARY_CLOUD_NAME, 
  api_key: process.env.CLOUDINARY_API_KEY, 
  api_secret: process.env.CLOUDINARY_API_SECRET 
});

/**
 * Utility function to upload a local file to Cloudinary
 * @param {string} localFilePath - The path of the staged file on the server disk
 */
const uploadOnCloudinary = async (localFilePath) => {
  try {
    if (!localFilePath) return null;

    // Upload the file onto Cloudinary
    const response = await cloudinary.uploader.upload(localFilePath, {
      resource_type: "auto" // Automatically detect if the asset is an image, video, raw file, etc.
    });

    // File has been uploaded successfully
    console.log("File uploaded successfully on Cloudinary:", response.url);
    
    // Clean up local file system path as it's safely in the cloud
    fs.unlinkSync(localFilePath);
    return response;

  } catch (error) {
    // If the upload operation failed, remove the locally saved temporary file to maintain server space
    if (fs.existsSync(localFilePath)) {
      fs.unlinkSync(localFilePath);
    }
    console.error("Cloudinary upload failed:", error);
    return null;
  }
};

export { uploadOnCloudinary };

```

> **File System Cleanup Tip:** Notice the use of `fs.unlinkSync(localFilePath)`. In low-level file system terminology, deleting a file is referred to as "unlinking". Running this synchronously during a catch block ensures that malicious or corrupted files don't permanently stall out or pile up on your backend storage array if an external network timeout occurs.

* * *

## 3\. Configuring the Upload Middleware (`multer.middleware.js`)

File tracking is an episodic requirement—you need it when a user submits a registration form with a profile image, but you don't need it when they are simply logging in with a text-based email and password. Because of this, file handling is best structured as a pluggable **Middleware**. Think of a middleware as a gatekeeper: *"Before executing the core controller logic, meet with me first to extract and organize the incoming binary files."*

We will configure Multer to utilize its `diskStorage` engine to capture incoming file buffers and place them inside a temporary `./public/temp` directory on our machine.

### Code Implementation

```javascript
import multer from "multer";

// Define where files should go and how they are named
const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    // Stage all uploads inside the local public/temp directory
    cb(null, "./public/temp");
  },
  filename: function (req, file, cb) {
    // For baseline configurations, retain the file's original name
    // Optimization Note: In final production systems, consider prepending a unique nanoID 
    // or timestamp suffix to prevent files with identical names from overwriting each other.
    cb(null, file.originalname);
  }
});

export const upload = multer({ 
  storage 
});

```

* * *

## 4\. Integrating the Workflow with Express Routes

With our staging middleware (`upload`) and cloud synchronization utility (`uploadOnCloudinary`) complete, tying file capabilities into any targeted route controller is incredibly elegant and modular.

```javascript
import { Router } from "express";
import { upload } from "../middlewares/multer.middleware.js";
import { registerUser } from "../controllers/user.controller.js";

const router = Router();

// Injecting the Multer upload middleware cleanly into an endpoint
router.route("/register").post(
  upload.fields([
    { name: "avatar", maxCount: 1 },
    { name: "coverImage", maxCount: 1 }
  ]),
  registerUser
);

export default router;

```

Inside your downstream `registerUser` controller, you can access the staged local directory strings using `req.files`, pull out the `path` values, and pass them directly into your asynchronous `uploadOnCloudinary(localFilePath)` handler.

* * *

## Conclusion

Building backends to scale requires thinking beyond standard CRUD scenarios. Taking the extra time to properly map out structural utilities, sandbox raw data flows into local disk queues, isolate credentials via environment keys, and integrate cleanup processes ensures that your architecture is robust and ready for production demands.