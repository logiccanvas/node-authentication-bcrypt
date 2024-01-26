# Node.js Authentication App

This is a simple Node.js application for user authentication with password login. The app uses the bcrypt package for password encryption and decryption.

#### Setup your app

```
const express = require("express");
const app = express();
const bcrypt = require("bcrypt");

app.use(express.json());
```

#### Assign users

> Note users are saved in local variable for demo purpose only.

```
const users = [];
```

### Routes

#### get users

```
app.get("/users", (req, res) => {
  res.json(users);
});
```

#### Save a user

```
app.post("/users", async (req, res) => {
  try {
    const salt = await bcrypt.genSalt();
    const hashedPassword = await bcrypt.hash(req.body.password, salt);

    const user = { name: req.body.name, password: hashedPassword };
    users.push(user);
    res.status(201).send();
  } catch (err) {
    res.status(500).send();
  }
});
```

##### Notes on Salt and Hashed Password

> **Salt:**

    - A salt is a random value that is generated uniquely for each password. It is then combined with the user's actual password before hashing.
    - The purpose of a salt is to add randomness and uniqueness to the hashing process. This helps in preventing attackers from using precomputed tables (rainbow tables) to easily crack passwords for multiple users simultaneously.
    - Salting ensures that even if two users have the same password, their hashed passwords will be different due to the unique salt.

> **Hashed Password:**

    - Hashing is a one-way cryptographic function that transforms input data (in this case, the combination of the password and salt) into a fixed-length string of characters, which appears random.
    - Hashed passwords are irreversible, meaning it's computationally infeasible to retrieve the original password from the hashed value. This property is crucial for security because even if the hashed passwords are exposed, attackers should find it challenging to reverse the process and obtain the actual passwords.
    - Hashed passwords are stored in databases rather than plain-text passwords, providing an additional layer of security.

Here, a unique salt is generated using bcrypt.genSalt(), and then it's combined with the user's password using bcrypt.hash() to create a hashed password. The salt is typically stored along with the hashed password in the database so that it can be used during password verification (during login attempts). This adds an extra layer of security to protect user passwords in the event of a data breach.

#### Login User

```
app.post("/users/login", async (req, res) => {
  const user = users.find((user) => user.name === req.body.name);

  if (!user) {
    return res.status(404).send("User not found");
  }

  try {
    const pass = await bcrypt.compare(req.body.password, user.password);
    const message = pass ? "Success" : "Not Allowed";

    res.send(message);
  } catch (e) {
    res.status(500).send();
  }
});
```

#### Listen to HTTP requests on port 3000

```
app.listen(3000);
```

### API Endpoints
(Check on postman or your favorite rest server)

#### 1. Get Users

- Endpoint: /users
- Method: GET
- Description: Retrieve a list of all users.

#### 2. Create User

- Endpoint: /users
- Method: POST
- Description: Create a new user with a hashed password.
- Request Body:
  ```
  {
      "name": "username",
      "password": "userpassword"
  }
  ```

#### 3. User Login
- Endpoint: /users/login
- Method: POST
- Description: Authenticate a user based on their credentials.
- Request Body:
    ```
    {
    "name": "username",
    "password": "userpassword"
    }
    ```
