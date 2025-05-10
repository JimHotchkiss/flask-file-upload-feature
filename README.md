**Flask File Upload Boilerplate**

A minimal, yet fully functional Flask application that demonstrates how to build a secure file‑upload feature using Flask-WTF and WTForms. This boilerplate is organized for clarity and ease of customization, and includes:

---

## 📁 Project Structure

```
project_root/
│
├── main.py            # Core application logic
├── templates/
│   └── index.html     # Upload form template
└── static/
    ├── files/         # Uploaded files destination
    └── css/
        └── style.css  # (Optional) Stylesheet for form styling
```

---

## 🛠️ Key Components

### 1. `main.py`

* **Flask App Setup**

  * Initializes a Flask application with `SECRET_KEY` for CSRF protection.
  * Configures `UPLOAD_FOLDER` to `static/files`, ensuring all uploads are stored in a public‑accessible directory.

* **Form Definition**

  ```python
  class UploadFileForm(FlaskForm):
      file = FileField("File", validators=[InputRequired()])
      submit = SubmitField("Upload File")
  ```

  * Uses **Flask-WTF** and **WTForms** to generate and validate the upload form.
  * Enforces that a file must be provided before submission.

* **Route Handling**

  ```python
  @app.route("/", methods=["GET", "POST"])
  @app.route("/home", methods=["GET", "POST"])
  def home():
      form = UploadFileForm()
      if form.validate_on_submit():
          file = form.file.data
          filename = secure_filename(file.filename)
          destination = os.path.join(os.path.abspath(os.path.dirname(__file__)),
                                     app.config["UPLOAD_FOLDER"], filename)
          file.save(destination)
          return "File has been uploaded"
      return render_template('index.html', form=form)
  ```

  * **GET** requests render the upload form.
  * **POST** requests:

    1. Validate form input.
    2. Sanitize the filename via `werkzeug.utils.secure_filename`.
    3. Save the file under `static/files`.
    4. Return a confirmation message.

* **Entry Point**

  ```python
  if __name__ == "__main__":
      app.run(debug=True)
  ```

  * Launches the Flask development server in debug mode for rapid iteration.

---

### 2. `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Flask‑File Load</title>
  <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
</head>
<body>
  <h1>Flask – Upload File Tutorial</h1>
  <form method="POST" enctype="multipart/form-data">
    {{ form.hidden_tag() }}
    {{ form.file }}
    {{ form.submit }}
  </form>
</body>
</html>
```

* **Form Attributes**

  * `enctype="multipart/form-data"`: Required for file uploads.
  * `{{ form.hidden_tag() }}`: Embeds CSRF token and any hidden fields.
  * WTForms fields (`{{ form.file }}`, `{{ form.submit }}`) render the input controls.

* **Styling Hook**

  * References `static/css/style.css` for customizing the look-and-feel of the form and page layout.

---

## 🛡️ Security Best Practices

* **CSRF Protection:** Enabled via `SECRET_KEY` and Flask-WTF’s hidden CSRF token.
* **Filename Sanitization:** Prevents directory traversal and unsafe filenames using `secure_filename`.
* **Static Folder Isolation:** All uploads go to `static/files`—no arbitrary file-system writes.

---

## 🚀 Getting Started

1. **Install dependencies**

   ```bash
   pip install flask flask-wtf wtforms
   ```
2. **Run the app**

   ```bash
   python main.py
   ```
3. **Visit** `http://localhost:5000/` to test the file‑upload form.

---

This boilerplate provides a solid foundation for any Flask application requiring file uploads—easily extendable to handle multiple file fields, custom validation, or integration with cloud storage services.
