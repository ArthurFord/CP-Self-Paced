Day 2
====================
# Connecting React to Django

Now that we've learned how to make a Django API (we deployed it a little while ago) and learned how to create a React frontend, we are going to connect them together.  Today, we'll create a very simple app that will display our wines and give the ability to create a new wine.

Like always, let's create a new React app called Wine Frontend:

```sh
$ npx create-react-app wine-frontend
```

After `cd`-ing into the repo, let's open it up and get React Router set up to take care of routes for us. First, let's get the dependencies installed:

```sh
$ npm install react-router-dom --save
$ npm install react-bootstrap --save
$ npm install react-bootstrap-table --save
```

In `App.js`, let's add in the following:

```javascript
import React, { Component } from 'react'
import { BrowserRouter, Route } from 'react-router-dom'
import './App.css'
import HomePage from './pages/HomePage.js'
import WinePage from './pages/WinePage.js'

class App extends Component {
  render() {
    return (
      <div>
        <BrowserRouter>
          <div>
            <Route exact path="/" component={HomePage} />
            <Route exact path="/wines/:wineID" component={WinePage} />
          </div>
        </BrowserRouter>
      </div>
    )
  }
}

export default App
```

Next, we'll create a folder called `pages` and in that folder, `HomePage.js` and `WinePage.js`. `HomePage.js` should read as follows:

```javascript
import React, { Component } from 'react'

class HomePage extends Component {
  render() {
    return (
      <div>
        <h1> Home Page </h1>
      </div>
    )
  }
}

export default HomePage
```

`WinePage.js` should read:

```javascript
import React, { Component } from 'react'

class WinePage extends Component {
  render() {
    return (
      <div> 
        <h1> Wine Page </h1>
      </div>
    )
  }
}

export default WinePage
```

Let's ensure that we can hit those pages before we do anything: 

```sh
npm start
```

Next, let's get an API set up in our React app to talk with our previously built Django Wine app - `src/api/WineAPI.js`:

```javascript
const fetchWineByID = (wineID) => {
  return fetch(`https://cors-anywhere.herokuapp.com/https://wineapi-cp.herokuapp.com/wines/${wineID}`)
    .then((response) => response.json())
}

const fetchWines = () => {
  return fetch(`https://cors-anywhere.herokuapp.com/https://wineapi-cp.herokuapp.com/wines`)
    .then((response) => response.json())
}

export default {
  fetchWineByID,
  fetchWines
}
```

The `cors-anywhere` link is required for us because we're making a request to our Heroku App via `localhost` and not on Heroku's domain. More reading can be found [here](https://medium.com/netscape/hacking-it-out-when-cors-wont-let-you-be-great-35f6206cc646).

Next, we need to hook up our `HomePage.js` to our API. This page will list out all of our wines with links to each individual wine. In that file, let's add the following:

```javascript
import React, { Component } from 'react'
import WineList from '../components/WineList/WineList.js'
import WineAPI from '../api/WineAPI.js'

class HomePage extends Component {
  state = {
    wines: []
  }

  componentDidMount(){
    WineAPI.fetchWines()
      .then((apiResponseJSON) => {
        this.setState({
          wines: apiResponseJSON
        })
      }
    )
  }

  render() {
    return (
      <div>
        <h1> All Wines </h1>
        <WineList wines={this.state.wines} />
      </div>
    )
  }
}

export default HomePage
```

You'll notice that I added a new component: `WineList`. Let's create that file and add the following code inside of it:

```javascript
import React, { Component } from 'react'
import { BootstrapTable, TableHeaderColumn } from 'react-bootstrap-table'

class WineList extends Component {
  render() {
    return (
      <div>
        <BootstrapTable data={this.props.wines}>
          <TableHeaderColumn isKey dataField='id'> ID </TableHeaderColumn>
          <TableHeaderColumn dataField='name'> Name </TableHeaderColumn>
          <TableHeaderColumn dataField='price'> Price </TableHeaderColumn>
          <TableHeaderColumn dataField='varietal'> Varietal </TableHeaderColumn>
          <TableHeaderColumn dataField='description'> Description </TableHeaderColumn>
        </BootstrapTable>
      </div>
    )
  }
}

export default WineList
```

If you visit [http://localhost:3000](http://localhost:3000), you'll see a list of all the wines. Success! Next, let's move onto seeing an individual wine. In `WinePage.js`, let's add the following code:

```javascript
import React, { Component } from 'react'
import WineAPI from '../api/WineAPI.js'

class WinePage extends Component {
  state = {
    wine: {}
  }

  componentDidMount() {
    const id = this.props.match.params.wineID
    WineAPI.fetchWineByID(id)
      .then((wine) => this.setState({
        wine: wine
    }))
  }

  render() {
    const wine = this.state.wine
    return (
      <div>
        <h2> Name </h2>
        <p> {wine['name']}</p>
        <h2> Price </h2>
        <p> {wine['price']}</p>
        <h2> Varietal </h2>
        <p> {wine['varietal']}</p>
        <h2> Description </h2>
        <p> {wine['description']}</p>
      </div>
    )
  }
}

export default WinePage
```

Visit http://localhost:3000/wines/1 to ensure that this works.

Lastly, let's create a form to create a new wine. First in `App.js`, we need to create a new route:

```javascript
import React, { Component } from 'react'
import { BrowserRouter, Route } from 'react-router-dom'
import './App.css'
import HomePage from './pages/HomePage.js'
import WinePage from './pages/WinePage.js'
import AddWinePage from './pages/AddWinePage.js'

class App extends Component {
  render() {
    return (
      <div>
        <BrowserRouter>
          <div>
            <Route exact path="/" component={HomePage} />
            <Route exact path="/add-wine" component={AddWinePage} />
            <Route exact path="/wines/:wineID" component={WinePage} />
          </div>
        </BrowserRouter>
      </div>
    )
  }
}

export default App
```

From there, let's add the method to talk to our API in `WineAPI.js`:

```javascript
const fetchWines = () => {
  return fetch(`https://cors-anywhere.herokuapp.com/https://wineapi-cp.herokuapp.com/wines`)
    .then((response) => response.json())
}

const fetchWineByID = (wineID) => {
  return fetch(`https://cors-anywhere.herokuapp.com/https://wineapi-cp.herokuapp.com/wines/${wineID}`)
    .then((response) => response.json())
}

const addWine = (wineObject) => {
  return fetch('https://cors-anywhere.herokuapp.com/https://wineapi-cp.herokuapp.com/wines/', {
    headers: {
      'Content-Type': 'application/json'
    },
    method: 'POST',
    body: JSON.stringify(wineObject)
  })
}

export default {
  fetchWineByID,
  fetchWines,
  addWine
}
```

Let's create a `AddWinePage.js` like we declared in `App.js`:

```javascript
import React, { Component } from 'react'
import { Form, Button } from 'react-bootstrap'
import WineAPI from '../api/WineAPI.js'
import { Redirect } from 'react-router'
class AddWinePage extends Component {
  state = {
    redirect: false
  }
  
  handleSubmit(event){
    event.preventDefault()
    const wineObject = {
      name: event.target.elements[0].value,
      price: event.target.elements[1].value,
      varietal: event.target.elements[2].value,
      description: event.target.elements[3].value
    }
    WineAPI.addWine(wineObject)
      .then((response) => { this.setState({ redirect: true }) })
  }

  render() {
    const { redirect } = this.state
    if (redirect) {
      return <Redirect to = "/" />
    }
    
    return (
      <div>
        <Form onSubmit={this.handleSubmit.bind(this)}>
          <Form.Group controlId="name">
            <Form.Label>Wine Name</Form.Label>
            <Form.Control/>
          </Form.Group>

          <Form.Group controlId="varietal">
            <Form.Label>Wine Varietal</Form.Label>
            <Form.Control/>
          </Form.Group>

          <Form.Group controlId="price">
            <Form.Label>Wine Price</Form.Label>
            <Form.Control/>
          </Form.Group>

          <Form.Group controlId="description">
            <Form.Label>Wine Description</Form.Label>
            <Form.Control/>
          </Form.Group>

          <Button variant="primary" type="submit">
            Submit
          </Button>
        </Form>
      </div>
    )
  }
}

export default AddWinePage
```

Try adding a new wine and see that everything works! It's terribly ugly, but we have officially set up our Django API and connected it to a brand new React application. Let's get deployment to Heroku working using the same steps as yesterday. Are you able to make requests online? Probably not. Let's find out why!

## Allow Django to receive CORS requests
Due to web security, Django doesn't allow requests from CORS like we have in our API. We're going to change a few settings using [Django CORS Headers](https://github.com/ottoyiu/django-cors-headers/) so that it does. In your Django app from a while back, follow these steps:
1. Install Django CORS Headers
    ```sh
    $ cd whatever_path_to_your_app
    $ source venv/bin/activate
    $ pip install django-cors-headers
    $ pip freeze > requirements.txt
    ```
2. Add it to your installed apps
    ```python
    MIDDLEWARE = [
        # make sure these two lines come first
        'corsheaders.middleware.CorsMiddleware', 
        'django.middleware.common.CommonMiddleware',
        ...
    ]
    ```
3. Add your website to the whitelist so that only CORS requests from your website are allowed in `settings.py`:
    ```
    CORS_ORIGIN_WHITELIST = ('your-full-site-no-https-//.herokuapp.com')
    ```

Commit these changes and push to Heroku master to see your app work!
