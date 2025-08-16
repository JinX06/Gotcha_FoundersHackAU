# FounderHack - Team Mokmau

Welcome to Team Mokmau's submission for FounderHack.

## About FounderHack

FounderHack is a hackathon hosted by NextGen Ventures that brings together innovators, entrepreneurs, and technologists to solve real-world challenges and build the next generation of startups.

## Team Mokmau

Team Mokmau is passionate about using technology to create practical, impactful solutions. Our team combines skills in software development, design, product thinking, and business strategy.

## Project Overview

**Project Name:** Gotcha!

**Challenge Statement:** Academic integrity tools often punish first and ask questions later. We wanted to build the opposite: a system that helps verify the author, not just judge the answer.

The idea for Gotcha came from seeing classmates and lecturers struggle with existing detection tools. Genuine work can be flagged incorrectly, students can lose trust in the process, and educators can be left with time-consuming manual checks.

Our key insight is that every student already has a writing or coding style. If we can establish a baseline of that style under invigilated conditions, we can compare future submissions against it and flag suspicious drift with context while keeping a human in the loop.

## Prototype Disclaimer

Gotcha is a small-scale hackathon prototype built to demonstrate the core idea, workflow, and technical feasibility of style-based authorship verification.

This project is not production-ready and should not be used as a final decision-making tool for academic misconduct cases. The current version is intentionally minimal and was developed within the time constraints of FounderHack. Some features are simplified, the dataset is synthetic, and the interface is designed mainly for demonstration purposes rather than polished real-world deployment.

The goal of this prototype is to show how a human-first academic integrity system could work: establishing a student's baseline style, comparing future submissions, surfacing explainable evidence, and supporting educators with signals rather than automatic verdicts.

Further validation, security hardening, privacy review, model calibration, bias testing, larger real-world datasets, and institutional policy alignment would be required before any production use.

## Solution

Gotcha helps educators assess authorship consistency through explainable style profiling and AI-assist likelihood scoring.

### 1. Baseline the Student's Style

In an invigilated setting, a student submits an essay or code sample. Gotcha extracts transparent, privacy-conscious features such as:

- Type-token ratio
- Average sentence length
- Punctuation and capitalisation ratios
- Lines of code
- Comment and blank-line ratios
- Naming patterns
- Simple complexity proxies

These features form the student's baseline style profile.

### 2. Compare Future Work Against the Baseline

For each new submission, Gotcha recomputes the same features and runs two checks:

1. **Style drift:** Measures how far the new submission differs from the student's baseline across linguistic and code-formatting dimensions.
2. **AI-assist likelihood:** Uses a Vertex AI AutoML Tabular classifier trained on labelled human and AI-assisted examples to produce calibrated class probabilities.

### 3. Human-First Decisioning

Gotcha does not automatically penalise students. Instead, it provides educators with:

- An AI usage score with an adjustable threshold
- Evidence showing which features drifted and by how much
- Suggested next steps, such as a short viva, live code reproduction, or draft provenance review

Educators remain in control, and students are shown constructive, non-accusatory language.

## Architecture

Gotcha uses a simple and practical architecture:

- **Ingest / Upload:** Student files are stored in MongoDB, and raw text or code is parsed server-side.
- **Feature Extraction:** Lightweight and explainable metrics are extracted without storing raw text in the model.
- **Modeling:** Vertex AI AutoML Tabular is used with predefined splits and endpoint deployment.
- **API:** A `/predict` endpoint supports both baseline registration and comparison scoring.
- **UI:** Teachers can select uploaded files, run comparisons, and view JSON-based evidence.

## Why Gotcha Is Fairer

- **Personalisation:** Gotcha checks consistency against a student's own baseline rather than relying only on general-purpose AI detection.
- **Explainability:** Features are interpretable and transparent, avoiding opaque black-box decisions.
- **Safety:** Model version, threshold, features, and evidence are logged for auditing.
- **Human oversight:** Results are framed as signals, not verdicts.

## What We Built During the Hackathon

During FounderHack, we built:

- An end-to-end pipeline from data preparation to AutoML training, endpoint deployment, and real-time prediction
- A robust server with timeouts, health checks, baseline fast-path support, and a manual file picker for demos
- Synthetic but realistic datasets to satisfy AutoML's 1,000-row minimum and demonstrate the full workflow

## Roadmap

Future improvements include:

- Replacing or augmenting simple metrics with privacy-preserving style embeddings stored as vectors only
- Adding student-wise splits and per-course thresholds to reduce leakage and improve calibration
- Supporting batch scoring and dashboards with precision/recall curves and style drift over time
- Adding optional integrations with LMS platforms, plagiarism databases, and IDE telemetry with explicit student consent

## How to Run

### Important Notes

If you do not have an existing MongoDB instance and want to set one up locally, follow the MongoDB installation guide.

If you prefer using MongoDB Atlas, create a free account and follow the Atlas setup guide.

If you do not want to set up a database, you can skip the login page by visiting:

Teacher Dashboard:

```text
http://<your-ip-address>:<port>/teacher/mrsmith
```

Student Dashboard:

```text
http://<your-ip-address>:<port>/student/mrsmith
```

### Prerequisites

Make sure you have the following installed:

- Node.js 18 or above
- Git
- Google Cloud CLI with access to Vertex AI
- MongoDB, either local or Atlas

This app uses:

- Express
- Tailwind CSS
- Mongoose
- dotenv

### Setup Instructions

#### 1. Clone the Repository

```bash
git clone https://github.com/JinX06/team_mokmau_foundershack.git
cd team_mokmau_foundershack
```

#### 2. Install Dependencies

```bash
npm install express
npm install sharp
npm install tailwindcss
npm install mongoose
```

Alternatively, if all dependencies are already listed in `package.json`, run:

```bash
npm install
```

#### 3. Set Up Environment Variables

Create a `.env` file in the root directory. You can use `.env.example` as a reference.

```env
# ===== App =====
PORT=3000
BASE_URL=http://localhost:3000

# ===== MongoDB =====
MONGO_URI=mongodb://127.0.0.1:27017/gotcha_dev

# ===== Google Cloud / Vertex AI =====
GCP_PROJECT=your-gcp-project-id
VERTEX_REGION=us-central1
VERTEX_ENDPOINT_ID=xxxxxxxxxxxxxxxxxx
VERTEX_PREDICT_TIMEOUT_MS=25000

# Option A: Use Application Default Credentials
# gcloud auth application-default login

# Option B: Use a Service Account
# GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json
```

#### 4. Authenticate with Google Cloud

```bash
gcloud auth application-default login
gcloud config set project your-gcp-project-id
```

#### 5. Set Up MongoDB

Create a database named:

```text
gotcha_dev
```

Then create a collection named:

```text
submissions
```

#### 6. Set Up Vertex AI

The model must be deployed in Vertex AI Endpoints.

After deployment:

1. Copy the Vertex AI Endpoint ID.
2. Add it to your `.env` file as `VERTEX_ENDPOINT_ID`.
3. Ensure the prediction schema matches the training features, including column names and data types.

## Technologies Used

- **Python** - Backend logic, feature extraction, and API development
- **MongoDB** - Storage for student submissions and style profiles
- **Google Cloud Vertex AI** - AutoML Tabular modeling and deployment
- **JavaScript & Node.js** - Server-side application and API endpoints
- **Express** - RESTful API framework
- **HTML & Tailwind CSS** - Frontend interface for educators and students
- **Mongoose & Multer** - MongoDB object modeling and file upload handling
- **Regex** - Text and code parsing for feature extraction
- **ChatGPT & GitHub Copilot** - Assisted development and prototyping
- **Canva** - Design and prototyping
- **Google Docs** - Documentation and collaboration
- **GitHub** - Version control and collaboration
- **VS Code** - Development environment

## Team Members

- Ong Jin Xiang - Project Lead, Product Strategy & Full-Stack Development
- Wong Weng Hin - Project Support
- Foo Yu Jun - Project Support
- Ng Yu Xuan - Project Support

## Contact

For questions or collaboration, contact:

```text
jong0092@student.monash.edu
```

## Additional Information

For Vertex AI API usage, refer to the official documentation:

[Vertex AI Documentation](https://cloud.google.com/vertex-ai/docs)

If access to the Vertex AI API is required, please contact the email listed above.

---

Thank you for checking out Gotcha by Team Mokmau!
