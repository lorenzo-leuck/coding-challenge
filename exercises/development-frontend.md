
31. Create a React component that renders a button with a click counter.

```js
import { useState } from "react"

export default function App(){
const [count, setCount] = useState(0)


function incrementValue() {
    const newValue = count + 1
    setCount(newValue)
}

return (
    <>
    <div> {count} clicks</div>
    <button onClick={incrementValue}>Click here! </button>
    </>
)

}

```

32. Build a form with controlled inputs for name and email.
33. Fetch data from an API and render it as a list of cards.

```js
import { useEffect, useState } from "react"
import axios from "axios"

export default function App() {

const [data, setData] = useState([])

useEffect(()=>{

async function getData() {
    const response = await axios.get("http://localhost:8000/api/cards")
    setData(response.data)
}

getData()
},[])

function renderCards(){
    if (data) {
    return data.map((element, index) =>  (
        <div key={index}>{element.label}</div>
    ))

    } else {
        return <div>No data</div>
    }
}

return (
    <> 
    
    {renderCards()}

    </>
)

}
```


34. Show a loading spinner while fetching data from an endpoint.
35. Add a type to a `User` object with fields `name`, `email`, `id`.
36. Create a dropdown menu to filter items by status.
37. Build a progress bar that updates based on polling a backend.
38. Make a POST request from React to start a background job.
39. Add error handling to an API call and display error messages.
40. Build a React hook that fetches and returns data from any URL.
41. Create a component that shows image thumbnails from a list.
42. Build a modal that shows image metadata when clicked.
43. Add sorting by match score to a gallery view.
44. Create tabs to toggle between Approved / Disqualified / All items.
45. Implement infinite scroll or "Load More" button with pagination.