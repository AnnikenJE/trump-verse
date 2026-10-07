# TrumpVerse

**Disclaimer:** The case was provided by the school. The project focuses solely on fullstack web development and does not express political opinions or support.

A fullstack web application for managing a merchandise catalogue. Users can browse the full range of items, search by name, add new merch with an image, and edit or delete existing entries. Built as a React and TypeScript frontend on top of a .NET Web API with a SQLite database, with the API documented in Swagger and in a static documentation page served from `wwwroot`.

## Features

- **Browse:** home page listing every item in the catalogue as responsive cards
- **Search:** look up merch by name, with partial and case-insensitive matching
- **Create:** form for name, price, category, description and product image
- **Edit:** look up an item by ID, change any field, and optionally replace its image
- **Delete:** remove an item, with a confirmation step before the request is sent
- **Image upload:** files are posted as `multipart/form-data` and stored under `wwwroot/images`
- Full CRUD across the stack, with shared state held in a React context so the list stays in sync

## Tech Stack

| Layer | Tools |
|---|---|
| Language | TypeScript, C# |
| Frontend | React 18, Vite 5 |
| Routing | React Router 7 |
| HTTP client | Axios |
| Styling | Bootstrap 5, CSS3, Font Awesome |
| Backend | ASP.NET Core 8 Web API |
| Data access | Entity Framework Core 9 |
| Database | SQLite |
| API docs | Swagger (Swashbuckle), static page in `wwwroot` |
| Linting | ESLint, typescript-eslint |

## Architecture

```
trumpverse/                     React frontend (Vite)
  src/pages/                    Home, Search, Create, Edit
  src/routing/AppRouting.tsx    Routes, shared header and footer
  src/components/merch/         MerchList, MerchItem, SearchMerch, CreateMerch, EditMerch
  src/components/shared/        MainHeader, MainNavigation, MainFooter
  src/contexts/MerchContext.tsx Merch state shared across pages
  src/services/MerchService.ts  Axios calls against the API
  src/interfaces/               IMerch, IMerchContext, IProps

TrumpVerseAPI/                  .NET Web API
  Controllers/                  TrumpMerchController, UploadImageController
  Contexts/TrumpContext.cs      EF Core DbContext
  Models/TrumpMerch.cs          Merch entity
  Interfaces/ITrumpMerch.cs     Entity contract
  Migrations/                   EF Core migrations (v1, v2, V3)
  Database/TrumpVerse.db        SQLite database file
  wwwroot/                      Static API documentation page and uploaded images
```

Components never call the API directly, every read and write goes through `MerchContext`, which delegates to `MerchService` and returns plain typed objects. The controllers take `TrumpContext` through constructor injection and wrap each action in error handling that maps failures to the matching HTTP status code.

**API endpoints**

| Method | Route | Purpose |
|---|---|---|
| `GET` | `/api/TrumpMerch` | All merch |
| `GET` | `/api/TrumpMerch/{id}` | One item by ID |
| `GET` | `/api/TrumpMerch/GetByName/{name}` | Items whose name contains `name` |
| `POST` | `/api/TrumpMerch` | Create an item |
| `PUT` | `/api/TrumpMerch` | Update an item |
| `DELETE` | `/api/TrumpMerch/{id}` | Delete an item |
| `POST` | `/api/UploadImage` | Upload a product image |
| `PUT` | `/api/UploadImage` | Replace a product image |

A `TrumpMerch` holds `Id`, `Name`, `Price`, `Category`, `Description` and `Image`, where `Image` is the file name of a picture served from `wwwroot/images`.

## Getting Started

**Requirements:** .NET 8 SDK and Node.js 18+ with npm.

```bash
git clone git@github.com:AnnikenJE/trump-verse.git
cd trump-verse
```

Start the backend first, the frontend expects it on `http://localhost:5290`:

```bash
cd TrumpVerseAPI
dotnet restore
dotnet run                 # API on http://localhost:5290, Swagger at /swagger
```

Then start the frontend in a second terminal:

```bash
cd trumpverse
npm install
npm run dev                # Vite dev server, opens in the browser
npm run build              # type-check and build for production
npm run lint               # ESLint
```

CORS is open to any origin in development, so no proxy configuration is needed. The SQLite file is committed with the migrations already applied; run `dotnet ef database update` if you need to rebuild it from scratch.

The API base URL is set in `trumpverse/src/services/MerchService.ts` and must be changed there if the backend is served from a different port.

## Background

Originally built as the exam project for **Web Development (15 ECTS)**, Kristiania University College, graded **A**.

All code is handwritten.
AI is only used to help write the README and commit messages.
