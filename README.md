# Feedback Application using JSON Server VS MongoDB Atlas Backend

## Table of Contents

- [Description](#description)
- [Installation](#installation)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Description

The key difference between the two versions of the code when using JSON Server and when getting the response from the backend (in this case, MongoDB Atlas) lies in how the backend responds to the frontend requests, particularly in how the `id` property of the feedback items is handled.

### When using JSON Server

- JSON Server automatically generates an `id` property for each item in the JSON response. These `id` properties are simple numeric values (e.g., 1, 2, 3, ...).
- In the `FeedbackContext`, you are directly using this automatically generated `id` property to uniquely identify and update the feedback items in the `feedback` state array.

```jsx
const updateFeedback = async (id, itemUpdate) => {
  // ... fetch and update logic ...
  setFeedback((prevFeedback) =>
    prevFeedback.map((item) => {
      return item.id === id ? { ...item, ...data } : item;
    })
  );
};
```

Below is a quick summary:

- With MongoDB Atlas, the `id` property of each feedback item is represented by the `_id` property generated by MongoDB. The `_id` property is an object identifier (ObjectId) in MongoDB, which is a complex type.
- In the `FeedbackList` component, you are now using the `_id` property instead of the `id` property to uniquely identify the feedback items.

```jsx
{
  feedback.map((feedbackItem) => {
    return <FeedbackItem item={feedbackItem} key={feedbackItem._id} />;
  });
}
```

The key difference in the two versions is how the `id` property is represented and used. In the JSON Server version, `id` is a simple numeric value, while in the MongoDB Atlas version, `_id` is a more complex ObjectId.

To make the MongoDB Atlas version work correctly, you made the following changes:

- Modified the `FeedbackItem` component to use `feedbackItem._id` as the key.
- In the `updateFeedback` function in `FeedbackContext`, use `item._id` instead of `item.id` to match the feedback item with the correct `_id` from the backend.

Additionally, the `deleteFeedback` function has been updated as follows for the MongoDB Atlas version:

```jsx
const deleteFeedback = (id) => {
  setShowDeleteModal(true);
  setItemToDelete(id);
};

const handleDeleteConfirmed = async () => {
  await fetch(`/feedback/${itemToDelete}`, {
    method: 'DELETE',
  });

  setShowDeleteModal(false);
  setFeedback((prevFeedback) => {
    return prevFeedback.filter((item) => {
      return item._id !== itemToDelete;
    });
  });
};
```

These changes were necessary because the MongoDB `_id` is a more complex data type, and it needs to be handled appropriately when deleting the feedback items from the state.

Please refer to the repository's README.md for further details and troubleshooting tips.

### When using a Backend with MongoDB Atlas

The code snippet below illustrates the process of acquiring the identical code from this repository onto your local machine.

```bash
# Clone the remote Git repository
git clone https://github.com/techstackmedia/react-front-to-back.git

# Change the current directory to the cloned repository's directory
cd react-front-to-back

# Install project dependencies using npm
npm install

# Start the application server
npm start

# List all remote branches
git branch -r

# Switch to a specific branch (replace <branch-name> with the desired branch name)
git checkout <branch-name> # for example: git checkout 35-refactor
```

The provided commands are used to clone a remote Git repository, navigate to the cloned repository's directory, install its dependencies, start the application server, list remote branches, and switch to a specific branch.

Here's a breakdown of each command:

1. `git clone https://github.com/techstackmedia/react-front-to-back.git`: This command clones the remote Git repository from the URL `https://github.com/techstackmedia/react-front-to-back` to your local machine. It creates a new directory named `react-front-to-back.git` and copies the entire repository contents into that directory.

2. `cd react-front-to-back.git`: This command changes the current working directory to the `react-front-to-back.git` directory. After executing this command, you will be inside the project directory.

3. `npm install`: This command installs the dependencies required by the application. It reads the `package.json` file in the project directory and installs all the packages listed in the `dependencies` and `devDependencies` sections.

4. `npm start`: This command starts the application server. The specific behavior of this command depends on how it is configured in the `package.json` file. Typically, it will run the application server and make it accessible at a specific port, allowing you to interact with the application in your web browser.

5. `git branch -r`: This command lists all the remote branches in the Git repository. Remote branches are branches that exist on the remote repository (in this case, on GitHub) and not on your local machine.

6. `git checkout <branch-name>`: This command is used to switch to a specific branch. Replace `<branch-name>` with the name of the branch you want to switch to. After executing this command, you will be on the specified branch, and you can start working on that branch.

In summary, these commands are commonly used to clone a Git repository, set up a local development environment by installing dependencies, start the application server, and switch to a specific branch to work on a particular feature or bug fix.

## Installation

To run the project on your local machine, follow these steps:

1. Clone the repository: `git clone https://github.com/techstackmedia/react-front-to-back`
2. Navigate to the project directory: `cd react-front-to-back`
3. Install dependencies: `npm install` or `yarn install`
4. Start the development server: `npm start` or `yarn start`

## Usage

To access the backend code that uses Express.js and Mongoose to connect to MongoDB Atlas, please visit the repository at [feedback-application-server](https://github.com/techstackmedia/feedback-application-server). You can find detailed instructions in the README.md file on how to set up and run the backend server.

Here are the steps to get started:

1. Clone the Repository:

   ```bash
   git clone https://github.com/techstackmedia/feedback-application-server.git
   cd feedback-application-server
   ```

2. Read the README.md:
   Take your time to read the README.md file in the repository. It contains important information about installing dependencies, setting up the MongoDB Atlas connection, and running the server.

3. Set Up MongoDB Atlas:
   Make sure you have a MongoDB Atlas account and create a cluster where your data will be stored. The README.md will guide you through setting up the connection to MongoDB Atlas in the server.

4. Install Dependencies:
   Run the following command to install the required dependencies for the backend server:

   ```bash
   npm install
   ```

5. Run the Backend Server:
   To start the backend server locally, use the following command:

   ```bash
   npm start
   ```

   If you prefer to test the application locally with the frontend, you can keep the proxy in the frontend's package.json file. It is useful during development to avoid CORS issues when making API requests.

6. Remove Proxy for Deployment (Optional):
   If you plan to deploy the backend and frontend separately, you can remove the proxy configuration from the frontend's package.json file. This is necessary when using a secure URL for the API in the deployed environment.

7. Adjust Proxy Port (Optional):
   By default, the proxy in the frontend's package.json is set to `http://localhost:5000`, which matches the backend's default port. If you wish to run the backend server on a different port, make sure to adjust the proxy in the package.json to the corresponding port.

   > **Note:**
   >
   > If you are not using the JSON-server, you can optionally delete the `db.json` file in the root directory.

Now you have the backend code up and running, and it is connected to the MongoDB Atlas database. The frontend should communicate with the backend API seamlessly, allowing you to test the complete application locally or deploy it to your preferred hosting service.

> **Note:**
>
> When deploying the backend, ensure that you have the necessary environment variables and their corresponding values set in the `.env` file on the deployment platform. However, it is important to never set the backend's port in the environment variable of your deploying platform. This caution also applies to the frontend.

Below is an example of having the API URL in the environment variable, whether locally or in a deploying platform's environment variable:

```txt
REACT_APP_URL_API=https://feedback.api.onrender.com/feedback # This is a randomly generated URL.
```

Make sure to use the prefix `REACT_APP` in your environment variables for your front-end React application.

By following this practice, you can avoid potential issues and ensure a successful deployment of your application.

Please refer to the repository's README.md for further details and troubleshooting tips.

Happy coding!

## Contributing

If you'd like to contribute to this project, follow these steps:

1. Fork the repository.
2. Create a new branch: `git checkout -b feature/new-feature`.
3. Make your changes and commit them: `git commit -m "Add some feature"`.
4. Push to the branch: `git push origin feature/new-feature`.
5. Create a pull request.

Please make sure to follow the project's coding guidelines and conventions when contributing.

## License

This project is licensed under the [MIT License](https://opensource.org/licenses/MIT).
