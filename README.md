# 🏠 Come And Live — Airbnb Clone

A full-stack property listing and booking platform inspired by Airbnb. Users can browse, create, edit, and delete property listings, leave reviews with ratings, and manage their accounts — all wrapped in a clean, server-rendered UI.

---

## 📸 Features

- **Browse Listings** — View all available property listings with images, prices, and locations
- **Search** — Search listings by title, location, or country
- **Create Listings** — Authenticated users can add new listings with image upload (via Cloudinary)
- **Edit & Delete Listings** — Only the listing owner can modify or remove their listing
- **Reviews & Ratings** — Logged-in users can leave reviews (1–5 star rating + comment) on any listing
- **Delete Reviews** — Only the review author can delete their review
- **User Authentication** — Sign up, log in, and log out with session-based auth (Passport.js)
- **Authorization Middleware** — Owner-only editing/deleting, author-only review deletion
- **Flash Messages** — Success and error notifications across all actions
- **Image Uploads** — Images stored on Cloudinary via Multer
- **Server-Side Validation** — Joi schema validation for listings and reviews
- **Error Handling** — Custom error class with a dedicated error page

---

## 🛠️ Tech Stack

| Layer          | Technology                                                    |
| -------------- | ------------------------------------------------------------- |
| **Runtime**    | [Node.js](https://nodejs.org/)                                |
| **Framework**  | [Express.js](https://expressjs.com/) v5                       |
| **Database**   | [MongoDB](https://www.mongodb.com/) via Mongoose              |
| **Templating** | [EJS](https://ejs.co/) + [ejs-mate](https://github.com/JacksonTian/ejs-mate) (layouts) |
| **Auth**       | [Passport.js](http://www.passportjs.org/) (Local Strategy) + passport-local-mongoose |
| **File Upload**| [Multer](https://github.com/expressjs/multer) + [Cloudinary](https://cloudinary.com/) |
| **Validation** | [Joi](https://joi.dev/)                                       |
| **Sessions**   | [express-session](https://github.com/expressjs/session) + [connect-flash](https://github.com/jaredhanson/connect-flash) |
| **Env Config** | [dotenv](https://github.com/motdotla/dotenv)                  |

---

## 📁 Folder Structure

```
airbnb2/
├── app.js                 # Entry point — Express app setup, middleware, DB connection
├── cloudconfig.js         # Cloudinary + Multer storage configuration
├── middleware.js           # Auth & authorization middleware (isLoggedIn, isOwner, etc.)
├── schema.js              # Joi validation schemas for listings & reviews
├── package.json
├── .env                   # Environment variables (not committed)
├── .gitignore
│
├── models/
│   ├── listing.js         # Mongoose schema for Listing
│   ├── review.js          # Mongoose schema for Review
│   └── user.js            # Mongoose schema for User (passport-local-mongoose)
│
├── routes/
│   ├── listing.js         # CRUD routes for listings
│   ├── review.js          # Create & delete routes for reviews
│   └── user.js            # Signup, login, logout routes
│
├── views/
│   ├── error.ejs          # Error page
│   ├── layouts/           # EJS layout templates (boilerplate)
│   ├── includes/          # Reusable partials (navbar, footer, etc.)
│   ├── listings/          # index, show, new, edit views
│   └── users/             # signup, login views
│
├── public/                # Static assets (CSS, JS, images)
├── uploads/               # Local upload directory
├── init/
│   ├── data.js            # Seed data
│   └── index.js           # Database seeding script
│
└── utils/
    ├── expressError.js    # Custom error class
    └── wrapAsync.js       # Async error wrapper for route handlers
```

---

## 🚀 Getting Started

### Prerequisites

- **Node.js** (v18 or higher recommended)
- **MongoDB** running locally on `mongodb://127.0.0.1:27017`
- A **Cloudinary** account (for image uploads)

### 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/airbnb2.git
cd airbnb2
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Configure Environment Variables

Create a `.env` file in the root directory with the following keys:

```env
CLOUD_NAME=your_cloudinary_cloud_name
CLOUD_API_KEY=your_cloudinary_api_key
CLOUD_API_SECRET=your_cloudinary_api_secret
SECRET=your_session_secret
```

### 4. Seed the Database (Optional)

Populate the database with sample listings:

```bash
node init/index.js
```

### 5. Start the Server

```bash
node app.js
```

The app will be running at **http://localhost:3000**

---

## 🔗 API Routes

### Listings

| Method   | Route                  | Description                | Auth Required |
| -------- | ---------------------- | -------------------------- | :-----------: |
| `GET`    | `/listings`            | View all listings          |      ❌       |
| `GET`    | `/listings/new`        | Render new listing form    |      ✅       |
| `POST`   | `/listings`            | Create a new listing       |      ✅       |
| `GET`    | `/listings/:id`        | View a specific listing    |      ❌       |
| `GET`    | `/listings/:id/edit`   | Render edit form           |  ✅ (Owner)   |
| `PUT`    | `/listings/:id`        | Update a listing           |  ✅ (Owner)   |
| `DELETE` | `/listings/:id`        | Delete a listing           |  ✅ (Owner)   |

### Reviews

| Method   | Route                              | Description        | Auth Required  |
| -------- | ---------------------------------- | ------------------ | :------------: |
| `POST`   | `/listings/:id/reviews`            | Add a review       |       ✅       |
| `DELETE` | `/listings/:id/reviews/:reviewId`  | Delete a review    |  ✅ (Author)   |

### Users

| Method | Route     | Description           | Auth Required |
| ------ | --------- | --------------------- | :-----------: |
| `GET`  | `/signup` | Render signup form    |      ❌       |
| `POST` | `/signup` | Register a new user   |      ❌       |
| `GET`  | `/login`  | Render login form     |      ❌       |
| `POST` | `/login`  | Log in a user         |      ❌       |
| `GET`  | `/logout` | Log out current user  |      ✅       |

---

## 🗃️ Data Models

### Listing

| Field       | Type              | Description                          |
| ----------- | ----------------- | ------------------------------------ |
| `title`     | String (required) | Name of the property                 |
| `description` | String (required) | Property description               |
| `location`  | String (required) | City / area                          |
| `country`   | String            | Country of the property              |
| `price`     | Number (required) | Price per night                      |
| `image`     | Object            | `{ filename, url }` — Cloudinary URL |
| `owner`     | ObjectId → User   | The user who created the listing     |
| `reviews`   | [ObjectId → Review] | Array of associated reviews        |

### Review

| Field       | Type              | Description              |
| ----------- | ----------------- | ------------------------ |
| `comment`   | String (required) | Review text              |
| `rating`    | Number (1–5)      | Star rating              |
| `createdAt` | Date              | Auto-generated timestamp |
| `author`    | ObjectId → User   | The user who wrote it    |

### User

| Field      | Type              | Description                             |
| ---------- | ----------------- | --------------------------------------- |
| `email`    | String (required) | User email                              |
| `username` | String            | Auto-managed by passport-local-mongoose |
| `password` | Hash              | Auto-managed by passport-local-mongoose |

---

## 🧩 Key Middleware

| Middleware         | Purpose                                                       |
| ------------------ | ------------------------------------------------------------- |
| `isLoggedIn`       | Redirects unauthenticated users to `/login`                   |
| `saveRedirectUrl`  | Preserves the original URL so users return after login         |
| `isOwner`          | Ensures only the listing owner can edit/delete                 |
| `isReviewAuthor`   | Ensures only the review author can delete their review         |
| `validateListing`  | Joi schema validation for listing data                         |
| `validateReview`   | Joi schema validation for review data                          |

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **ISC License**.

---

> Built with ❤️ as a full-stack learning project.