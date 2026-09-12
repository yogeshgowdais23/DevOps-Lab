# Exercise 5: Docker Security with AppArmor and Python

## Files

- `app.py`
- `Dockerfile`
- `my-apparmor-profile`
- `apply_apparmor.py`
- `test_restricted_actions.py`

## Run the Flask Application

Build the image:

```bash
docker build -t flask-apparmor .
```

Run the application with AppArmor:

```bash
docker run --security-opt="apparmor=my-apparmor-profile" -p 5000:5000 flask-apparmor
```

Test the application:

```bash
curl http://localhost:5000
```

## Apply AppArmor Using Python

```bash
python apply_apparmor.py
```

## Test Restricted Actions

```bash
python test_restricted_actions.py
```

## Screenshots

![Dockerfile](screenshots/01-dockerfile.png)

![Flask application](screenshots/02-app-source.png)

![Docker image build](screenshots/03-image-build.png)

![Image build complete](screenshots/04-image-build-complete.png)

![AppArmor Python script](screenshots/05-apply-apparmor-script.png)

![AppArmor script output](screenshots/06-apply-apparmor-output.png)

![AppArmor profile](screenshots/07-apparmor-profile.png)

![Flask running](screenshots/08-flask-running.png)

![Restricted actions test](screenshots/09-restricted-actions-output.png)

![Docker build log](screenshots/10-image-build-log.png)
