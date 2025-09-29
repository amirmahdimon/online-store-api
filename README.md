# Online Store API

A robust REST API backend for an e-commerce application built with Node.js, Express, and MongoDB. This server provides all the necessary endpoints to power an online store, including product management, user authentication, order processing, and payment integration.

## Features

- 📱 Product Management
- 🛍️ Category and Subcategory Management
- 🏷️ Brand Management
- 🎯 Variant and Variant Type Support
- 👤 User Authentication and Management
- 🛒 Order Processing
- 💳 Payment Integration with Stripe
- 🔔 Push Notifications using OneSignal
- 📸 File Upload Support for Product Images
- 🎫 Coupon Code System

## Tech Stack

- **Runtime Environment:** Node.js
- **Framework:** Express.js
- **Database:** MongoDB with Mongoose ODM
- **File Upload:** Multer
- **Payment Processing:** Stripe
- **Push Notifications:** OneSignal
- **API Security:** CORS enabled
- **Environment Variables:** dotenv

## Prerequisites

- Node.js (v14 or higher)
- MongoDB Database
- Stripe Account (for payments)
- OneSignal Account (for notifications)

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd online_store_api
```

2. Install dependencies:
```bash
npm install
```

3. Create a `.env` file in the root directory with the following variables:
```env
MONGO_URL=your_mongodb_connection_string
STRIPE_SECRET_KEY=your_stripe_secret_key
ONESIGNAL_APP_ID=your_onesignal_app_id
ONESIGNAL_API_KEY=your_onesignal_api_key
```

4. Start the server:
```bash
node index.js
```

## API Documentation

### Authentication
#### Register User
```http
POST /users/register
```
Body:
```json
{
    "name": "string",
    "email": "string",
    "password": "string",
    "phone": "string"
}
```
Response:
```json
{
    "success": true,
    "user": {
        "id": "string",
        "name": "string",
        "email": "string",
        "phone": "string"
    },
    "token": "string"
}
```

#### Login
```http
POST /users/login
```
Body:
```json
{
    "email": "string",
    "password": "string"
}
```
Response:
```json
{
    "success": true,
    "token": "string"
}
```

### Categories
#### Get All Categories
```http
GET /categories
```
Response:
```json
{
    "success": true,
    "categories": [
        {
            "id": "string",
            "name": "string",
            "image": "string",
            "subCategories": ["string"]
        }
    ]
}
```

#### Create Category
```http
POST /categories
Content-Type: multipart/form-data
```
Form Data:
- `name`: string
- `image`: file

Response:
```json
{
    "success": true,
    "category": {
        "id": "string",
        "name": "string",
        "image": "string"
    }
}
```

### Products
#### Get All Products
```http
GET /products
```
Query Parameters:
- `category`: string (optional)
- `brand`: string (optional)
- `search`: string (optional)
- `page`: number (optional)
- `limit`: number (optional)

Response:
```json
{
    "success": true,
    "products": [
        {
            "id": "string",
            "name": "string",
            "description": "string",
            "price": number,
            "images": ["string"],
            "category": "string",
            "brand": "string",
            "variants": [
                {
                    "type": "string",
                    "options": ["string"]
                }
            ]
        }
    ],
    "total": number,
    "page": number,
    "pages": number
}
```

#### Create Product
```http
POST /products
Content-Type: multipart/form-data
```
Form Data:
- `name`: string
- `description`: string
- `price`: number
- `category`: string
- `brand`: string
- `images[]`: file (multiple)
- `variants`: JSON string

Response:
```json
{
    "success": true,
    "product": {
        "id": "string",
        "name": "string",
        "description": "string",
        "price": number,
        "images": ["string"],
        "category": "string",
        "brand": "string"
    }
}
```

### Orders
#### Create Order
```http
POST /orders
Authorization: Bearer {token}
```
Body:
```json
{
    "items": [
        {
            "product": "string",
            "quantity": number,
            "variants": {
                "color": "string",
                "size": "string"
            }
        }
    ],
    "shippingAddress": {
        "address": "string",
        "city": "string",
        "postalCode": "string"
    }
}
```
Response:
```json
{
    "success": true,
    "order": {
        "id": "string",
        "items": [],
        "total": number,
        "status": "string",
        "paymentStatus": "string"
    }
}
```

#### Get Order Details
```http
GET /orders/{orderId}
Authorization: Bearer {token}
```
Response:
```json
{
    "success": true,
    "order": {
        "id": "string",
        "items": [],
        "total": number,
        "status": "string",
        "paymentStatus": "string",
        "shippingAddress": {}
    }
}
```

### Payments
#### Process Payment
```http
POST /payment
Authorization: Bearer {token}
```
Body:
```json
{
    "orderId": "string",
    "paymentMethod": {
        "type": "card",
        "card": {
            "number": "string",
            "exp_month": number,
            "exp_year": number,
            "cvc": "string"
        }
    }
}
```
Response:
```json
{
    "success": true,
    "paymentId": "string",
    "status": "succeeded"
}
```

### Coupon Codes
#### Apply Coupon
```http
POST /couponCode/apply
Authorization: Bearer {token}
```
Body:
```json
{
    "code": "string",
    "orderId": "string"
}
```
Response:
```json
{
    "success": true,
    "discount": number,
    "newTotal": number
}
```

## File Structure

```
├── index.js           # Entry point
├── model/             # Database models
├── routes/            # API routes
├── public/            # Static files (images)
│   ├── products/
│   ├── category/
│   └── posters/
└── uploadFile.js      # File upload configuration
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the ISC License.

## Support

For support, please open an issue in the repository or contact the development team.
