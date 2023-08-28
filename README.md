# Feedback App CRUD Operation with JSON Server

## Table of Contents

- [Description](#description)
- [Issue](#issue)
- [Installation](#installation)
- [Usage](#usage)
- [Issue](#issue)
- [Contributing](#contributing)
- [License](#license)

## Description

There's a React application for managing feedback data. The application interacts with a backend server, which is a JSON server, to fetch, add, update, and delete feedback items. Let's break down the code and explain each part.

1. `package.json`:

The `package.json` file contains scripts to run the application and the JSON server concurrently during development. The script `"server": "json-server --watch db.json --port 5000"` is used to start the JSON server, which watches the `db.json` file and runs on port 5000. The `"dev": "concurrently \"npm start\" \"npm run server\""` script is used to run both the React application (using `npm start`) and the JSON server (using the previously defined `"server"` script) simultaneously.

```jsx
"scripts": {
  "server": "json-server --watch db.json --port 5000",
  "dev": "concurrently \"npm start\" \"npm run server\""
}
```

This means to run the application and the JSON server concurrently run `npm run dev`:

```bash
npm run dev
```

2. `FeedbackContext` and `FeedbackProvider`:

The `FeedbackContext` is a React context that provides a state and functions related to feedback management. The `FeedbackProvider` is a component that wraps its children with the context provider and holds the state for feedback, loading state, and other related variables and functions.

3. Fetching Data and Updating `db.json`:

The `useEffect` hook in the `FeedbackProvider` component is used to fetch data from the backend server (JSON server) when the component mounts. It fetches the feedback data from the endpoint `'http://localhost:5000/feedback?_sort=id&_order=desc'` and stores it in the `feedback` state using `setFeedback(data)`.

A conditional block checks if `redirectTo500` is `true`. If so, the code utilizes the `Navigate` component to redirect the user to the `/500` route. This approach effectively handles `redirection` based on the value of `redirectTo500`.

When new feedback is submitted using the `FeedbackForm` component, the `addFeedback` function is called. It sends a POST request to the backend with the new feedback data, and upon receiving the response, it adds the new feedback to the local `feedback` state using `setFeedback(updatedFeedbackArray)`.

Similarly, when feedback is edited using the `updateFeedback` function, it sends a PATCH request to the backend with the updated feedback data. Once the response is received, it updates the corresponding feedback item in the local `feedback` state using `setFeedback((prevFeedback) => prevFeedback.map(...))`.

When a feedback item is deleted using the `deleteFeedback` function, it sends a DELETE request to the backend with the ID of the item to be deleted. Upon successful deletion, it removes the corresponding feedback item from the local `feedback` state using `setFeedback((prevFeedback) => prevFeedback.filter(...))`.

4. `Pulse` Component:

The `Pulse` component is a simple component that displays an animated pulse icon.

5. `FeedbackList` Component:

The `FeedbackList` component displays a list of feedback items. It consumes the `FeedbackContext` to access the `feedback` state and the `isLoading` state. If there are no feedback items (`feedback` is null or has zero length), it displays a message saying "No Feedback Yet." If `isLoading` is true, it displays the `Pulse` component to indicate loading. Otherwise, it maps through the `feedback` array and renders `FeedbackItem` components for each feedback item.

6. `FeedbackForm` Component:

The `FeedbackForm` component is used for adding and editing feedback items. It uses the `useContext` hook to access the `addFeedback`, `updateFeedback`, and `feedbackEdit` variables from the `FeedbackContext`. The component includes a form with a rating selector and a text input for the feedback message. When the form is submitted, it calls the `handleFormSubmit` function, which checks the length of the text input and either adds or updates the feedback accordingly.

7. `db.json`:

The `db.json` file acts as the backend server's data store. It contains an array of feedback objects. The JSON server reads and updates this file to provide a RESTful API for the React application.

In summary, the React application interacts with the JSON server (backend) through various API calls (GET, POST, PATCH, DELETE) to manage feedback data. The `FeedbackProvider` component holds the state and functions for data management, and the other components use the `FeedbackContext` to access this shared state and perform CRUD operations.

The issue with the order of the items being added to the list might be related to the way JSON-server handles the creation of new items and assigns their IDs. JSON-server automatically assigns an ID to the newly created item, and by default, it increments the ID based on the existing maximum ID in the database.

Since you are using JSON-server, it's essential to check the behavior of JSON-server when creating new items. By default, JSON-server assigns a new item the next available ID, which would be the highest ID in the existing list plus one. This might result in the newly added item appearing at the bottom of the list, even though the code seems to add it to the top.

If you want to maintain a specific order when adding items to the list, you need to ensure that the server is providing the data in the desired order. In this case, you can modify your JSON-server data file to arrange the items in the order you want them to appear.

For the Feedback App, use `http://localhost:5000/feedback?_sort=id&_order=desc` to sort the id in descending order in the useEffect function, `getFeedback`

```jsx
const getFeedback = async () => {
  try {
    const response = await fetch(
      'http://localhost:5000/feedback?_sort=id&_order=desc',
      {
        method: 'GET',
      }
    );

    if (!response.ok) {
      throw new Error('Network response was not ok');
    }

    const data = await response.json();

    setFeedback(data);
    setIsLoading(false);
  } catch (error) {
    console.error('Error fetching data:', error);
    setIsLoading(false);
  }
};
```

With this approach, the new item will always be assigned a unique negative ID, ensuring it appears at the top of the list when displayed in the UI.

Keep in mind that this solution will work specifically with JSON-server and assumes that the ID field is unique and sortable (e.g., numbers or timestamps). If your API or backend has different constraints, you may need to adjust the approach accordingly.

Below is a quick summary:

The React application fetches data from the JSON server using the fetch API, and the JSON server reads and serves data from the db.json file. Feedback data is added, edited, and deleted by making POST, PATCH, and DELETE requests to the JSON server, which updates the db.json file accordingly.

## Installation

To run the project on your local machine, follow these steps:

1. Clone the repository: `git clone https://github.com/techstackmedia/react-front-to-back`
2. Navigate to the project directory: `cd react-front-to-back`
3. Install dependencies: `npm install` or `yarn install`
4. Start the development server: `npm start` or `yarn start`

## Usage

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

## Issue

In case you come across the error "Module not found: Error: Can't resolve 'package-name' in 'your/path/here'", address this by running the command `npm install` (and consider restarting the development server if needed). This action will guarantee the installation of the required dependencies, successfully resolving the issue.

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
