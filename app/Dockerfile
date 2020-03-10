# Build
FROM python:3.8.0-alpine as builder

# Github details for private repository
ARG GIT_PAK
ARG GIT_USR
ARG HOME
ARG APP_HOME

# set work directory
WORKDIR /usr/src/app

# set environment variables
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

# Install and update system dependencies
RUN apk update && apk add --no-cache libpq libffi-dev openldap-dev openssl-dev build-base python-dev py-pip jpeg-dev zlib-dev gcc libc-dev unixodbc-dev git

RUN pip install --upgrade pip

# Download project repository
RUN git clone https://github.com/suisuss/suss.space-pub.git
# Find a way to only get the requirements.txt

# Install project dependencies
WORKDIR /usr/src/app/suss.space-pub
RUN pip wheel --no-cache-dir --no-deps --wheel-dir /usr/src/app/wheels -r requirements.txt

# Deploy
FROM python:3.8.0-alpine

# Github details for private repository
ARG GIT_PAK
ARG GIT_USR
ARG HOME
ARG APP_HOME

# Add user app
RUN addgroup -S app && adduser -S app -G app

# Install system and project dependencies
RUN apk update && apk add --no-cache libpq libffi-dev openldap-dev openssl-dev build-base python-dev py-pip jpeg-dev zlib-dev gcc libc-dev unixodbc-dev git

COPY --from=builder /usr/src/app/wheels /wheels
COPY --from=builder /usr/src/app/suss.space-pub/requirements.txt .
RUN pip install --upgrade pip
RUN pip install --no-cache /wheels/*


# Download project repository
WORKDIR ${APP_HOME}
RUN git clone https://github.com/suisuss/suss.space-pub.git
# Where I'd have... 'RUN git clone https://${GIT_USR}:${GIT_PAK}@github.com/suisuss/suss.space-prod.git'

# Change ownership of project files to user
RUN chown -R app:app ${APP_HOME}/suss.space-pub

# Switch to app user
USER app

# Go into project directory and run project with gunicorn
WORKDIR ${APP_HOME}/suss.space-pub

COPY /gunicorn/config/gunicorn.conf.py .

CMD ["gunicorn", "-c", "gunicorn.conf.py", "run:app"]
