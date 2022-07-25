# General Assembly Project 2 - Fruity 4 You 

## Overview

A two day hackathon as a group of three, this was my first experience of creating a react front-end and using a public API.

Fruity 4 You is an app where you can create your own fruit salads and keep track of the nutritional information. 

Visit [Fruity 4 You](https://fruity-4-you.netlify.app/) 

### API used
https://www.fruityvice.com/

### Code installation
* Clone or download the repo
* Install Yarn with terminal command: yarn 
* Start server with terminal command: yarn start

### The brief
* **Consume a public API**
* **Have several components**
* **The app can have a router**
* **Include wireframes**
* **Be deployed online**

 
### Technologies used 
Front-end:
* React
* Axios
* SCSS
* Bootstrap

Dev tools:
* VS code
* Git 
* GitHub
* Insomnia
* Excalidraw (wireframes)
* Netlify (deployment)

## Process

### Getting started
This was a 48 hour hackathon so it was important to decide on an API as soon as possible. After all of us pitched some ideas, we decided on Fran’s idea of using the “fruityvice” API as we really liked the freedom it gave us to add a recipe-maker functionality to our app. 

However, after spending some time planning for our app, we came across a CORS error when trying to retrieve data with an axios request from the front-end. We then went away and started planning a Simpson’s app. However, then our tutor came back to us and showed us a workaround for the CORS error:
The address can be placed in the package.json as a proxy:
```ruby
"proxy": "https://www.fruityvice.com/",
```
The axios request then only needs:
```ruby
const { data } = await axios.get('/api/fruit/all')
```
This was great news as we were keen on getting the salad maker functionality going!

### Planning 

We used Excaidraw for our wireframes and also a bit of pseudocoding for the salad maker.

<img width="821" alt="Project 2 Excal" src="https://user-images.githubusercontent.com/101710474/180813664-a83ac4f0-43e8-4521-a82f-e7697ddce69e.png">
<img width="821" alt="Project 2 Excal2" src="https://user-images.githubusercontent.com/101710474/180814209-c9d32d55-cc35-4f14-b467-9f75d1ce485a.png">
<img width="821" alt="Project 2 Excal3" src="https://user-images.githubusercontent.com/101710474/180815061-a2a553c8-8d3b-4a2a-9b39-7662e65e6a47.png">


### Salad maker 

I spent the majority of my time on the salad-maker. It was really satisfying and fun to experiment with React’s useState as we had only just recently learnt about it. 

Here are the states used:
```ruby
 const [fruits, setFruits] = useState([])
 const [fruit1, setFruit1] = useState({
   name: '',
   calories: '',
   carbohydrates: '',
   fat: '',
   protein: '',
   sugar: '',
 })
 const [fruit2, setFruit2] = useState({
   name: '',
   calories: '',
   carbohydrates: '',
   fat: '',
   protein: '',
   sugar: '',
 })
 const [fruit3, setFruit3] = useState({
   name: '',
   calories: '',
   carbohydrates: '',
   fat: '',
   protein: '',
   sugar: '',
 })
 const [salad, setSalad] = useState({})

```

An axios request for all of the fruits is set to the “fruits” state:

```ruby
 useEffect(() => {
   const getFruits = async () => {
     try {
       const { data } = await axios.get('/api/fruit/all')
       setFruits(data)
     } catch (err) {
       console.log(err)
     }
   }
   getFruits()
 }, [])

```

All the “fruits” state is then actually used for is the three dropdown selects for choosing your fruits to put in the salad. For example, fruit 1:

```ruby
                 <label htmlFor='fruit1'>Choose 1st fruit </label>
                 <select type='text' name='fruit1' onChange={handleChange1}>
                   <option value='noselect'>------------</option>
                   {fruits.map(fruit => <option key={fruit.name} value={fruit.id}>{fruit.name}</option>)}
                 </select>

```

At first I wasn’t sure if this axios request was overkill, but I then felt confident it wasn’t, as these dropdowns could dynamically change along with the API. If more fruits are added, for example. 

On change, the dropdowns trigger more axios requests: one axios request for each fruit chosen. For example, here is the “handleChange” function for fruit 1:

```ruby
 const handleChange1 = async (e) => {
   // console.log(e.target.value)
   if (e.target.value !== 'noselect') {
     try {
       const { data } = await axios.get(`/api/fruit/${e.target.value}`)
       console.log(data)
       setFruit1({ ...fruit1, name: data.name, calories: data.nutritions.calories, carbohydrates: data.nutritions.carbohydrates, fat: data.nutritions.fat, protein: data.nutritions.protein, sugar: data.nutritions.sugar })
     } catch (err) {
       console.log(err)
     }
   }
 }

```

Once, each fruit has been chosen from a dropdown, the states of “fruit1”, “fruit2” and “fruit3” all contain that specific fruit’s nutritional data. 

The “Make Salad” button is then ready to be pressed. Here is the “handleSubmit” function which is triggered:

```ruby
 const handleSubmit = (e) => {
   e.preventDefault()
   if (Object.keys(fruit1).length === 0 || Object.keys(fruit2).length === 0 || Object.keys(fruit3).length === 0) {
     return
   } else {
     setSalad({
       calories: Math.round((fruit1.calories + fruit2.calories + fruit3.calories) / 3),
       carbohydrates: Math.round((fruit1.carbohydrates + fruit2.carbohydrates + fruit3.carbohydrates) / 3),
       fat: Math.round((fruit1.fat + fruit2.fat + fruit3.fat) / 3),
       protein: Math.round((fruit1.protein + fruit2.protein + fruit3.protein) / 3),
       sugar: Math.round((fruit1.sugar + fruit2.sugar + fruit3.sugar) / 3),
     })
   }
 }
```

With some simple maths, the “salad” state now contains a combination of the fruits’ nutritional info. The salad is then ready to be displayed to the user:

```ruby
       <div className='salad-info'>
         {/* <h3>Your {fruit1.name} & {fruit2.name} & {fruit3.name} fruit salad (300g/400g/500g/..)</h3> */}
         {salad ?
           <>
             <h4><span>🍽</span> Nutritional info (per 100g)</h4>
             <p><span>Carbohydrates: </span>{salad.carbohydrates}</p>
             <hr />
             <p><span>Protein: </span>{salad.protein}</p>
             <hr />
             <p><span>Fat: </span>{salad.fat}</p>
             <hr />
             <p><span>Calories: </span>{salad.calories}</p>
             <hr />
             <p><span>Sugar: </span>{salad.sugar}</p>
           </>
           :
           <h6>Select three fruits to add to your salad..</h6>
         }
         <button className='btn btn-warning w-100' onClick={reset}>Reset</button>
       </div>
```


### Salad maker displaying images

Fran then had the cool idea of displaying images of the fruits you choose as slots in a slot machine. This was group-coded by all of us just before hand in! 

We actually spent a couple hours on this and even had a few of the tutors stumped as to why it wasn’t working. 

```ruby
       <div className='flex-container'>
         <div className='slot1'>
           {fruit1.name !== '' && <img src={`../../images/${fruit1.name}.png`} alt={fruit1.name} />}
         </div>
         <div className='slot2'>
           {fruit2.name !== '' && <img src={`../../images/${fruit2.name}.png`} alt={fruit2.name} />}
         </div>
         <div className='slot3'>
           {fruit3.name !== '' && <img src={`../../images/${fruit3.name}.png`} alt={fruit3.name} />}
         </div>
       </div>

```

We were sure the code we had written should work. The routes to the images were correct and worked if we hard coded one image url. However, when we tried to dynamically write in the url using “fruit.name”, we received an error. We were eventually told that to dynamically change an image’s url in JSX, the images have to all be within the public folder. This solved it! 





## Bugs and Blockers

We lost quite a bit of time with the CORS and this set us back
Navbar should only display once user has scrolled down to it
On the index page, the links to individual fruits are wider than the actual cards which display the fruit


## Wins
We worked well as a team, splitting up tasks evenly and keeping in contact
This was also our first experience of group coding and taught us the value of it
We were very pleased to add the salad-maker functionality to our app as it gave it a real world use


## Future improvements
Register and log in
Add/ edit fruits
Smoothie-maker with possibilities of adding own ingredients with nutritional information (e.g. ice-cream)
