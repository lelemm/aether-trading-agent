############################
# Stage 1: Frontend build  #
############################
FROM node:20-alpine AS frontend-builder

WORKDIR /app/frontend

# Install dependencies
COPY frontend/package.json ./package.json
RUN npm install

# Copy source and build
COPY frontend/ ./
RUN npm run build


#################################
# Stage 2: Backend application   #
#################################
FROM python:3.11-slim AS backend

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install python dependencies
COPY backend/requirements.txt ./requirements.txt
RUN pip install --upgrade pip \
    && pip install --no-cache-dir -r requirements.txt \
    && rm -f requirements.txt

# Copy backend source code
COPY backend/ ./backend

# Copy built frontend into backend directory
COPY --from=frontend-builder /app/frontend/dist ./backend/frontend_dist

# Configure FastAPI to find the frontend bundle
ENV FRONTEND_DIST_PATH=/app/backend/frontend_dist

WORKDIR /app/backend

EXPOSE 8000

CMD ["uvicorn", "api_server:app", "--host", "0.0.0.0", "--port", "8000"]
