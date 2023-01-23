# Page Navigation
There are 3 ways to navigate through pages in Next Js, by `next/Link`, by `form submission`, by `useRouter`
1. Navigate by `next/Link`
Link can be added by `<Link href='destination'>Label</Link>`
2. Navigate by `form submission`
Traditional way to submit a form to the action
3. Navigate `useRouter`
```
const router = useRouter()
router.push('/distination')
```
This is different than the `router.replace('/distination')`, `push` is to push the next link to the history stack,
`Back` will go back to the previous page; `replace` is to replace the current path with the distination, `Back` will not
go back to the previous page.

# Next JS Tutorial
Next js has file based routing, and it has separate pages routing and api routing. We usually put the client functions
into the `pages` and the server calls involving external apis and database into `pages/api`.

# Connect to MongoDB
## Create a MongoDB Instance
Create an instance on Atlas, then get the connection string for NodeJs, it should be sth similar to these
`mongodb+srv://<username>:<password>@<project>.mbd86.mongodb.net/?retryWrites=true&w=majority`
## Add MongoDB to NextJs App
run `npm i mongodb` to install mongodb package to the project
## Set up connection in NextJs
### Create a Mongo connection client
Create a `lib` folder under project src, then add a `mongodb.ts` to wrap mongo client connection
```
import {MongoClient} from 'mongodb'

const uri = process.env.MONGODB_URI
const options = {
    useUnifiedTopology: true,
    useNewUrlParser: true
}

let client
let clientPromise

if (!process.env.MONGODB_URI) {
    throw new Error('Please add your Mongo URI to .env.local')
}

if (process.env.NODE_ENV === 'development') {
    // In development mode, use a global variable so that the value
  // is preserved across module reloads caused by HMR (Hot Module Replacement).
  if (!global._mongoClientPromise) {
    client = new MongoClient(uri, options)
    global._mongoClientPromise = client.connect()
  }
  clientPromise = global._mongoClientPromise
} else {
      // In production mode, it's best to not use a global variable.
  client = new MongoClient(uri, options)
  clientPromise = client.connect()
}

// Export a module-scoped MongoClient promise. By doing this in a
// separate module, the client can be shared across functions.
export default clientPromise
```
### Use Mongo on pages
The mongo can be connected on pages
```
import ClientPromise from '../lib/mongodb'

export default function Home({users}) {
    
    return (
        <div className='container'>
            <div>
            {users.map((user, index) => {
                return (
                    <div className='card' key={index}>
                        <h2>{user.name}</h2>
                        <p>{user.email}</p>
                    </div>
                    </div>
                )
            })}
            </div>
        </div>
    )
}

// add the db connection in the getServerSideProps
export async function getServerSideProps(context) {
    const client = await clientPromise
    
    const db = client.db('<db_name>')
    let users = await db.collection('users').find({}).toArray()
    users = JSON.parse(JSON.stringify(users))
    
    return {
        props: {users},
    }
}
```
### Use Mongo on apis
The mongo db connected on apis
```
import clientPromise from '../lib/mongodb'

export default async function handler(req, res) {
    
    const client = await clientPromise
    const db = client.db('<db_name>')
    
    switch (req.method) {
    case "POST":
      let bodyObject = JSON.parse(req.body);
      let newPost = await db.collection("posts").insertOne(bodyObject);
      res.json(newPost.ops[0]);
      break;
    case "GET":
      const posts = await db.collection("posts").find({}).toArray();
      res.json({ status: 200, data: posts });
      break;
  }
}

```

With all the api implemented, we can call the endpoint in pages
```
export async function getServerSideProps(context) {
  let res = await fetch("http://localhost:3000/api/posts", {
    method: "GET",
    headers: {
      "Content-Type": "application/json",
    },
  });
  let posts = await res.json();

  return {
    props: { posts },
  };
}
```

We can also create a post by making a post to the api
```
useEffect(() => {
  setPostsState(posts);
}, [posts]);

let submitForm = async (e) => {
  setLoading(true);
  e.preventDefault();
  let res = await fetch("http://localhost:3000/api/posts", {
    method: "POST",
    body: JSON.stringify({
      title: title,
      content: content,
    }),
  });
  res = await res.json();
  setPostsState([...postsState, res]);
  setTitle("");
  setContent("");
  setLoading(false);
};
```

The complete `posts.js` will be like
```
// pages/posts.js

import { useEffect, useState } from "react";

export default function Home({ posts }) {
  const [postsState, setPostsState] = useState([]);
  const [title, setTitle] = useState("");
  const [content, setContent] = useState("");
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    setPostsState(posts);
  }, [posts]);

  let submitForm = async (e) => {
    setLoading(true);
    e.preventDefault();
    let res = await fetch("http://localhost:3000/api/posts", {
      method: "POST",
      body: JSON.stringify({
        title: title,
        content: content,
      }),
    });
    res = await res.json();
    setPostsState([...postsState, res]);
    setTitle("");
    setContent("");
    setLoading(false);
  };

  return (
    <div className="container">
      <div>
        <div>
          {postsState.map((post, index) => {
            return (
              <div className="card" key={index}>
                <h2>{post.title}</h2>
                <p>{post.content}</p>
              </div>
            );
          })}
        </div>

        <div className="add-form">
          <form onSubmit={submitForm}>
            <input
              type="text"
              name="title"
              placeholder="Title"
              value={title}
              onChange={(e) => setTitle(e.target.value)}
            />
            <textarea
              type="text"
              name="content"
              rows="10"
              placeholder="Content"
              value={content}
              onChange={(e) => setContent(e.target.value)}
            />
            <button type="submit" disabled={loading ? true : false}>
              {loading ? "Adding" : "Add"}
            </button>
          </form>
        </div>
      </div>
    </div>
  );
}

export async function getServerSideProps(context) {
  let res = await fetch("http://localhost:3000/api/posts", {
    method: "GET",
    headers: {
      "Content-Type": "application/json",
    },
  });
  let posts = await res.json();

  return {
    props: { posts },
  };
}
```
### Add some styles 
```
.container {
  display: flex;
  justify-content: center;
}
.card {
  box-shadow: 0 2px 5px 0 #dfdfdf, 0 8px 40px 0 rgb(10 14 29 / 6%);
  padding: 1rem;
  border-radius: 0.5rem;
  width: 20rem;
  height: auto;
  margin-top: 2rem;
}
.card h2 {
  font-size: 2em;
}

.card p {
  font-size: 1.5em;
}
form {
  margin-top: 2rem;
}
input,
textarea {
  width: 20rem;
  padding: 1rem;
  font-size: 1.2em;
  display: block;
  margin: 1rem 0;
  border-radius: 0.5rem;
}

button {
  width: 100%;
  height: auto;
  padding: 1rem;
  font-size: 1.2em;
  background: #505050;
  color: white;
  border: none;
  border-radius: 0.5rem;
  cursor: pointer;
}

button:hover {
  background: #292929;
}

@media only screen and (max-width: 600px) {
  .card {
    width: 95%;
  }
  input,
  textarea {
    width: 95%;
  }

```

