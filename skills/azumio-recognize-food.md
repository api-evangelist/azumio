---
name: Recognize food in a photo with Calorie Mama
description: Submit a JPEG food photo to the Azumio Calorie Mama Food Recognition API and get back recognized foods with calories and macronutrients (or full nutrition).
api: openapi/azumio-food-recognition-openapi.yml
operations: [recognizeFood, recognizeFoodFull]
---

# Recognize food in a photo (Calorie Mama Food Recognition API)

Use the Azumio Calorie Mama Food Recognition API to identify food in an image and
return nutrition data.

## Prerequisites
- An API key (`user_key`). Sign up at https://dev.caloriemama.ai/signup and
  manage keys at https://dev.caloriemama.ai/login.
- A JPEG image of the food, ideally 544x544 pixels.

## Auth
Pass the key as the `user_key` query parameter on every request. There is no
OAuth, bearer token, or scope — API key only.

## Steps
1. Choose the operation:
   - `recognizeFood` (`POST /v1/foodrecognition`) — returns calories plus core
     macronutrients (totalFat, totalCarbs, protein).
   - `recognizeFoodFull` (`POST /v1/foodrecognition/full`) — returns the full
     17-field nutrition profile (vitamins, minerals, fat types).
2. Send the image either as `multipart/form-data` with a `media` file part, or
   as a raw `image/jpeg` binary body.
3. Read the response: a `model_version` plus a ranked list of food groups, each
   with candidate `FoodItem`s carrying `name`, `score`, optional `brand`, and
   `nutrition`.

## Example
```
curl -X POST \
  "https://api-2445582032290.production.gw.apicast.io/v1/foodrecognition?user_key=YOUR_KEY" \
  -F media=@meal.jpg
```

## Error handling
Errors use a custom envelope `{ "error": { "errorDetail": "...", "code": ### } }`
where `code` mirrors the HTTP status:
- `401` — invalid/missing `user_key`; fix the key.
- `409` — plan usage limit exceeded; upgrade or wait for reset.
- `429` — throttled; slow down and retry with backoff.
- `400` — bad image or request format.
- `500` — transient server error; retry with backoff.

See `errors/azumio-problem-types.yml` and `conventions/azumio-conventions.yml`.
