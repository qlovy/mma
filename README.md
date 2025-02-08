## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

## MMA

#### Introduction:
The goal of this project is to build a sport app for my training. I choose to use a PWAs App because I don't have to pass by the Apple Store or Google Play.
I choose Vue.js and Bootstrap because Vue make the creation of a PWA more simple. It's great to learn new things. For Bootstrap, he simplifies the creation of the design.

### Todos

- [x] Change the program "Poussée" --> make it correct
- [x] Add program "Tirage" and "Jambes"
- [x] Remove the time after last exercise
- [x] Add warm-up
- [ ] make that the sound is like the sound of a timer on iphone , so the music can restart. => looks complex for a simple PWA
- [x] Change the exercise while the rest time ?
- [x] Show the state of series ?
- [x] upgrade the information (repetitions in seconds, ...) ==> do a function
- [x] add a button for skip an exercise (like a help button...)
- [x] add advice for each exercises
- [x] add a timer for exercise with time (more than 10 seconds)
- [ ] change the background color on the top and bottom on iphone => looks complex
- [x] create components to simplify the App.vue (home page, exercise page, ...)
- [ ] make that we can skip a time recuperation / a series
- [x] make the icon menu in color when we are on it => change the fill color, [example](https://icons.getbootstrap.com/icons/gear/)
- [x] use local storage for storing exercises
- [x] JSON file or text file for add new exercise of configure multiple exercise --> one the local storage is ok
- [x] add a menu with two categories (one for exercising and the other for settings)
- [ ] add an option to make a circuit => Modify JSON structure, manage the detection of it
- [ ] simplify the creation of JSON file by using an interpreter like Traction, 5x6, 30s convert into the normal one. => looks for another type of config file
- [x] add the stretching => Modify JSON structure, manage the detection

### Ideas

- Create a tool for create sport program => this is possible by modifying the JSON file and then import it. => maybe simple with one config file ?

