---
title: "Building an E-commerce Platform: Purpose and Rails Model/Controller Design"
categories: [full-stack-development,API Development,Backend Development, Software Architecture]
tags: [ruby,api,ecommerce,devise,jwt,postgresql,rest,mvc]

---

# Street Fighter Ecommerce

# Overview
E-commerce has become a cornerstone of modern retail, and building a custom platform was my way of diving deep into full-stack development. My goal was to simulate this in a creative way with the development of a dynamic and scalable web application using Ruby on Rails, with a seamless user experience powered by React.js-- Oh and did I mention it is themed around the Street Fighter franchise? That's right! This project gave me the opportunity to combine some of my favorite things: Street Fighter and Web Development! This post covers the first steps: building the core backend infrastructure—starting with models in Rails. We have a lot to cover content-wise, so I'll be splitting this project into multiple posts. When building my e-commerce platform, I used several powerful technologies, each playing a unique role in bringing the application to life. Below, I’ll explain the purpose of each technology and how it was used to create the core features of the project. Let's hop into it!

# Technologies Used

## Ruby on Rails (Backend Framework)
Ruby on Rails is the backbone of the backend in this project. It is simple and efficient, though there are some performance trade-offs (more notably building your front end in Rails) in comparison to something like Node.js-- Rails allows for rapid development while maintaining clean, readable code. It follows the MVC (Model-View-Controller) architecture, which makes it easy to manage data, create routes, and interact with databases. Rails handles the logic behind key features like product listings, user authentication, and managing orders.

I chose Rails for its powerful built-in tools like ActiveRecord, which simplifies interactions with your database. It allows me to define complex relationships between models (users, products, and carts in this case) without writing tons of SQL queries manually. Rails also makes it easy to streamline setting up RESTful APIs, which were crucial for connecting the backend to the frontend.

## PostgreSQL (Database)
PostgreSQL is an open-source relational database that I used to store all the data for the e-commerce platform, including user information, product details, and orders. It was the perfect choice because of its ability to handle complex queries and support advanced data types and relationships. PostgreSQL works seamlessly with Rails through the ActiveRecord ORM (Object Relational Mapping), allowing me to interact with the database without needing to write raw SQL code often.

I opted for PostgreSQL because of its performance and scalability. As the platform grows, PostgreSQL can handle the increasing load, ensuring smooth performance. It’s also widely used in production applications, so it was important for me to use a reliable database solution.

## React.js (Frontend Library)
React.js is the powerhouse behind the dynamic user interface of the platform. And my go-to framework for most of my front-end projects! React allows me to build interactive and reusable UI components, making the app responsive and user-friendly. React also uses a virtual DOM, which optimizes performance by only updating elements that change rather than reloading the entire page.

React is a popular choice for modern web applications because of its fast rendering and component-based architecture. This made it ideal for features like product listings, which required dynamic updates as users browsed or filtered through products. React’s state management with hooks, and access to Redux, made it easier to handle complex interactions like updating the cart in real-time.

## Redux and React Hooks (State Management)
When dealing with complex applications like this e-commerce platform, managing the state of different components becomes tricky. That’s where Redux comes in. Redux is a state management library that works with React to manage and share the application’s state across multiple components. React Hooks still had use cases for state-management, just for simpler things like grabbing state from API calls and mapping said state over components like product lists.

I used Redux to manage global state, particularly for the shopping cart and user authentication. Instead of passing props down through multiple components, Redux allows me to store the state in a central place, making it easy to access and update throughout the app. This is perfect where context for users or carts needs to be retained through navigation, so I used it. For example, when users add items to their cart, Redux keeps track of the cart's state (what products you have and of what quantity) even as they navigate to different parts of the site.

## JWT (JSON Web Token) (Authentication)
Security is a top priority in any e-commerce platform, especially when dealing with user accounts and orders. I implemented JWT (JSON Web Token) for user authentication to ensure secure login and session management. JWT is a method for securely transmitting information between parties, which in this case, is between the client and the server.

I didn't initially choose JWT, as I was planning on using Devise's authentication system exclusively. However, using Rails solely for API development, I ran into some challenges with Devise (which I will get into later). JWT allows me to manage authentication without the need for traditional sessions or cookies. When a user logs in, they receive a token that’s stored on the client side (usually in local storage). This token is sent with every request after a user authenticates themselves, ensuring only authorized users can access protected routes like viewing their cart or placing an order.

## Rspec and Insomnia (Testing)
Testing is essential for ensuring the stability of the application, and for this project, I used RSpec and Insomnia for testing the backend. RSpec is a testing tool for Ruby that helps ensure the correctness of your models, controllers, and APIs. I wrote test cases to validate user authentication, product retrieval, and the cart’s functionality. Insomnia was used to test requests at each of my defined routes. The purpose is to mimic the user experience interacting with each route to test edge cases, and ensure each of my controller methods works correctly.

RSpec makes it easy to write human-readable test cases, allowing me to simulate user interactions and check that the expected data is returned. By using RSpec, I was able to catch issues early on and ensure that the core functionality remained intact as I built out more features. I used Insomnia by preference, though Postman or any other testing technology works too!

## Putting It All Together

By combining these technologies, I was able to create a full-stack e-commerce platform that handles everything from user authentication to secure transactions and product management. Each tool played a key role in shaping the final product, and together, they helped me deliver a robust, scalable solution! Now that we've gone over what was used, let's hop right into the development process.

---

## Breaking Down the User Model in Rails

```rb
class User < ActiveRecord::Base

  devise :database_authenticatable, :registerable, :recoverable, :rememberable, :trackable, :validatable, :jwt_authenticatable, jwt_revocation_strategy: JwtDenylist
  after_create :create_user_cart
  validates :email, presence: true, uniqueness: true, format: { with: URI::MailTo::EMAIL_REGEXP }
  has_many :orders
  has_many :cart_items
  has_one :cart

  private

  def set_uid
    self.uid = self.email
  end

  def create_user_cart
    Cart.create(user: self)
  end

  def self.ransackable_attributes(auth_object = nil)
    ["allow_password_change", "confirmation_sent_at", "confirmation_token", "confirmed_at", "created_at", "email", "encrypted_password", "id", "image", "name", "nickname", "provider", "remember_created_at", "reset_password_sent_at", "reset_password_token", "tokens", "uid", "unconfirmed_email", "updated_at"]
  end

end
```

To start, I defined all the models, starting with the model least dependent on the others: the User Model. 
As you can see in the coding example above-- the User model plays a critical role in managing user data, authentication, and relationships with other models like carts and orders. 

Here’s a breakdown of what each part of the code does and why it’s important:

### Devise Authentication

At the top of the User model, I use Devise, a powerful authentication solution for Rails applications. It provides many features to handle user authentication and security with minimal setup. Devise easily adds functionality like user registration, password recovery, session tracking, and validations (such as email and password checks) without needing to write all the code manually.

```rb
devise :database_authenticatable, :registerable, :recoverable, :rememberable, :trackable, :validatable, :jwt_authenticatable, jwt_revocation_strategy: JwtDenylist
```
You may be wondering why I included the jwt methods as well. This was actually one of the challenges I faced developing this project. My Rails app is API only, and Devise is typically session-based. Session management in Devise is designed for stateful instances in traditional web applications, where session data is stored on the server. This was incompatible with a RESTful API, which is stateless. So I needed to find a way to utilize Devise's effiecent authentication system, while also overcoming the limits of session based authentication. 

So I decided to implement token-based authentication using JSON Web Tokens (JWT). As mentioned in the first section, instead of storing sessions, JWT allows users to authenticate by receiving a token when they log in. This token is then included with every subsequent API request, allowing the server to verify the user without needing to manage session data. Devise's built-in jwt_authenticatable module helped integrate this process, but required careful configuration to handle the API's stateless nature.

```rb
:jwt_authenticatable, jwt_revocation_strategy: JwtDenylist
```

By implementing this solution, I was able to overcome the limitations of session-based authentication in an API-only environment and provide secure, scalable user authentication for the platform.

### Model Relationships
Below the Devise setup, I define the relationships between the User and other models in the application:

```rb
has_many :orders
has_many :cart_items
has_one :cart

```
The syntax is pretty straight-forward, these are common associations available in relational databases. User's can have many orders,cart-items, but carts are tied to one user.

Which is actually automatically done in the <i>after_create</i> method. It creates a new instance of a Cart and associates the new user with it.

```rb
def create_user_cart
  Cart.create(user: self)
end
```

### Setting User Id and Ransackable Attributes

The Model includes assigning the unique Identifier for the user to the email they signed up with, making it easier to track them within the system. 

Lastly it includes the Ransackable attributes. This is basically including all the attributes associated with the User Model, that the model is searchable by within the Ransack gem(A popular and powerful filtering gem). This was mostly for admin features.

```rb
def self.ransackable_attributes(auth_object = nil)
  ["allow_password_change", "confirmation_sent_at", "confirmation_token", ...]
end
```

# Next Post/Conclusion

This was a long post, so let us recap what we've covered so far: 
We covered the foundation of the Street Fighter E-commerce by diving into the technologies used, and the User model in Rails. We explored how Devise and JWT were implemented to manage authentication and session handling, within an API-only context. We also discussed how key relationships between users, carts, and orders were set up, allowing the backend to handle the interactions between different models seamlessly.

The challenges with session-based authentication in Devise led to an important solution involving JWTs, allowing for secure, stateless authentication across API requests. 

In the next post, we’ll dive into the controllers and routes, showing how the API handles incoming requests and interacts with the models we defined. This will include managing CRUD operations, securing routes with authentication tokens, and handling the business logic behind the scenes.

Stay tuned for more details on how the core features come together in the next stage of development!

