# OCR Scanner and Parser - Proof of concept

An integrated solution for scanning and parsing documents, using a combination of .NET Core, PostgreSQL, Angular, and more.
Written with learning in mind, more of an exercise than attempt at creating a fully fledged product.

This repository houses the CI/CD configurations and a global overview of the entire application.

## 📌 Table of Contents

- [Features](#features)
- [Architecture](#architecture)
- [Contributing](#contributing)

## 🚀 Features

### General

- Containerization of the app w/ Docker.
- Github Actions CI/CD workflows.
- CI pushing it's results to Docker Hub for potential, further use outside of current workflows.
- CD to Heroku as separate dynos.
  
### Backend

- Clean Architecture (CA) approach.
- Business logic orchestrated with a dedicated App layer.
- Mediator pattern w/ the use of MediatR.
- CQRS of handlers.
- Fluent tools integration: FluentValidation and FluentMigration.
- Dual database configuration with dedicated DbContexts.
- Authentication and authorization using Identity.
- Portable .pdf -> .jpeg conversion
- OCR document scan with the use of Tesseract and storage of structured results.
- Parsing of results by expandable fields of interest.
- SignalR hubs to notify about finished operations in real time.
- Standarized filtering, sorting and pagination for repositories.
  
### Frontend

- Developed using Angular with modular design.
- Incorporates Angular Material for faster development and more polished UI.
- Efficient CRUD operations for invoice management.
- Visualization tools for scanning and parsing statistics.
- SignalR integration for notifying when batch operations are done.

## 🏗 Architecture

### Backend

#### Clean Architecture

The backend is designed around the principles of Clean Architecture, ensuring the separation of concerns, scalability, and maintainability.
It consists of layers which pretty closely follow the ones defined by the theory:
- Domain: Core, innermost layer. Contains concrete entities to operate on further.
- Application: The most crucial layer, containing the contract for all of the required functionality. Because of that, it houses all of the project's business logic and orchestrates the work of DI'd services. Thanks to it, layers are able to interact w/ each other indirectly, keeping the reference management to minimum. 
- Persistence: Dedicated database management layer, contains migrations, main DbContext and project repositories.
- Infrastructure: Service layer, 
- TextRecognition, Automation: Service layers, could be a part of the Infrastructure, but kept them as they are for better service separation.
  
#### Database

There are two databases and contexts. Sole reason of it was keeping everything Identity related inside of the Infrastructure layer as it probably should be in theory.
- General Database: Contains most of the application data.
- Identity Database: Dedicated to user authentication and authorization.

#### Middleware

The ones most frequently used were MediatR behaviors. Thanks to them I'd implemented simple authorization, logging and validation processes for the handlers.

#### OCR & Parsing

Contract for the process consists of 4 interfaces
- IImageManipulatorService: Should identify files sent by the user and prepare them for scanning, as of right now converts .pdf to .jpeg.
- ITextRecognitionService: Needs to yield strongly typed scanning results from the provided file. Implemented with the use of Tesseract.
- IParsingRule: Represents a way of looking for desirable part of the text provided to it, I'd written simple ones which look up by regices or similarity.
- IParsingField: Represents the part of the info we're trying to read from the invoice e.g. payment date, payment type, type of the document etc. Separates the process of parsing into two stages, independent parsing and deduction from each other based on results from the first stage.

### Frontend

- Modularity: Thanks to the built in DI and routing of Angular, frontend is split by lazily loaded modules, one for each page
- Angular Material: Component library of choice, used throughout the whole app

### CI/CD

- Containerization of backend and frontend using Dockerfile in the root of their corresponding repos
- Separate, parent repo for CI/CD process, children projects nested as submodules
- Github Actions workflows for CI and CD
- .env variables kept locally for development or inside the main GH repo. They're passed to Heroku inside of the CD workflow, as they're needed.
  
## 💡 Contributing

While this is a solo project, I'm open to feedback, suggestions, and contributions. Please raise an issue or submit a pull request if you have something to share!
