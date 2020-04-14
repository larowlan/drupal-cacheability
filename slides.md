### Testing twig components <br/>with jest

---

### Overview

Note:

- Assumed knowledge
- Background
- JavaScript testing ecosystem
- Meat and potatoes
- Final word

---

### Assumed knowledge

Note:

- Modern JS features like 
> - object destructuring, 
> - arrow functions, 
> - esm
- familiar with the concept of component driven design

---

#### ♻️ PreviousNext front end <br/>workflow

* Style guide driven development https://youtu.be/uHWlUhvBwr0
* Twig ninja tactics https://youtu.be/HytOs7TS-FY
* Postcss https://postcss.org/
* Rollup https://rollupjs.org


Note:

- Front end devs create components and page mockups in styleguide
- JavaScript is scoped to the components
- backend devs integrate with Drupal
- some projects all-in on no HTML tags in Drupal templates, embedding/extending styleguide
- layout builder challenges

---

### but first .. React

Note:

- this story starts with react
- known state yields set of markup, state changes cause updates

---

### Twig starts out the same

Variables + Template + Logic = Markup

Note:

- twig has the same starting point, set variable, given markup
- you can test this with phpunit 
- does not require Drupal (even in D7)
- fast, robust

---

### but dynamic behaviours...

Note:

- but interactivity requires js
- written in vanilla js
- npm packages with esm
- roll-up to create transpiled version for use in browser/Drupal

---

### React testing envy 

Note:

- Solid testing tools based around jest and testing library org

---

### Jest

jestjs.io

Note:

- Test runner for JavaScript
- all the assertions you would expect
- mocking stubbing etc
- snapshot testing
- code coverage

---

### Example

sum.js
<pre><code class="language-javascript hljs">

const sum = function(a, b) {
  return a + b;
}

export default sum;

</code></pre>

---

### Test

<pre><code class="language-javascript hljs">

import sum from './sum';

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});

</code></pre>

---

### Testing react components

@testing-library/dom
@testing-library/react

https://testing-library.com/

Note:

- after enzyme effectively stopped working with hooks, has become the go-to

---

### Example

<pre><code class="language-javascript hljs">
import React from 'react'
import Counter from '.counter'
import { render, fireEvent } from '@testing-library/react'
import '@testing-library/jest-dom/extend-expect'

describe('clicking plus increments the counter', () => {
  const { container, getByText } = render(&lt;Counter label="Count" /&gt;)
  expect(container).toMatchSnapshot()
  fireEvent.click(getByText('Plus'))
  expect(container.querySelector('.counter__count')).toHaveContent(1)
})

</code></pre>

---

### Envious 😿?

Note:

- working on react projects makes it hard to come back to Drupal
- contrast that to how we do front end testing in Drupal?
- nightwatch
- phpunit + chrome
- functional testing is slowww

---

### What if 🤔?

- twigjs https://github.com/twigjs/twig.js

Note:

- kss styleguides with twigjs
- maybe we could leverage that?

---

### Enter twig-testing-library

<pre><code class="hljs language-bash">npm install --save-dev twig-testing-library</code></pre>

Note:

- same ergonomics but for twig

---

### Usage

<pre><code class="language-javascript hljs">
import {render, fireEvent} from "twig-testing-library"

describe('Some test suite', () => {
  it('should do something', async () => {
    const { getByText } = render('/some/file.twig', {
      some_variable: 'something',
    })
    const button = getByText('Some button')
    fireEvent.click(button)
    expect(getByText('Something else')).toBeTruthy()
  })
})
</code></pre>

---

### Let's run it  🤩

Note:

- fire up phpstorm, walk thorugh the files
- terminal - run jest

---

### If you leave with nothing else ☝️

<pre><code class="language-javascript hljs">
import {render, fireEvent} from "twig-testing-library"

describe('It should render', () => {
  const { container } = render('/some/file.twig')
  expect(container).toMatchSnapshot()
})
</code></pre>


Note:

- snapshot testing is the lowest barrier of entry to testing
- prevent broken markup
- back to phpstorm, fiddle with accordion__content
- rerun

---

### Other wins

Note:

- coverage 
- terminal - run coverage
- debugging support in phpstorm - breakpoints in your JS 

---

### Where's the Drupal? 💧

Note:

- you're probably thinking this is testing twig files in isolation, not Drupal
- this is part of your testing pyramid

---

### This pyramid

<img src="https://martinfowler.com/articles/practical-test-pyramid/testPyramid.png" alt="testing pyramid"/>

<div class="credit">https://martinfowler.com/articles/practical-test-pyramid.html</div>

Note:

- you need a mix
- these are fast and give coverage
- there are many moving parts
- you still need functional tests

---

### Maximize your return

Note:

- keep your js clean and vanilla
- keep your components atomic
- the closer your Drupal markup is to your styleguide, less chance of regression
- twig ninja tactics

---

### Disclaimers

Note:

- jest uses jsdom to implement browser apis
- twigjs is not the same twig Drupal is using, but its good enough

---

### Summing up

github.com/larowlan/twig-testing-library

Note:

- building with other tools can expose you to better ways of working
- this is early days for the package
- get involved
