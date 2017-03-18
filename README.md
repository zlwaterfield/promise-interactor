[![Build Status](https://travis-ci.org/interlock/promise-interactor.svg?branch=master)](https://travis-ci.org/interlock/promise-interactor)
[![Dependency Status](https://david-dm.org/interlock/promise-interactor.svg)](https://david-dm.org/interlock/promise-interactor.svg)

# Interactors

Based on the popular ruby gem [Interactor](https://github.com/collectiveidea/interactor). Uses chainable promises to allow
flow of completing multiple interactors.


## TODO

- Organizer pattern

## Single Pattern

```js
class AuthenticateUser extends Interactor{
  call() {
    User.find({email: context.email}).then((user) => {
      if (user.password === context.password) {
        this.context.user = user;
        this.success();
      } else {
        this.fail(new Error("Invalid password"));
      }
    }).error( (err) => {
      this.fail(err);
    });
  }
}

module.exports = AuthenticateUser;
```

Calling the interactor

```js
(new AuthenticateUser({email, password})).exec().then((interactor) {
  console.log(`User logged in: ${interactor.context.user});
});

```

## Rollback

If your interactor rejected, you can optionally provide a rollback which will clean up

```js
class AuthenticateUser extends Interactor{
  // If we returned a promise, it would wait on that before calling the reject
  // otherwise this is considered a sync call and the promise is rejected immediately after
  rollback(err) {
    console.log(err);
  }
}
```

## Organization Pattern

Makes grouping interactors in sequence a little easier.

class AuthUserOrganizer extends Organizer {
  constructor(context) {
    super(context);
    this.interactors = [AuthenticateUser, SomeOtherInteractor];
  }
}

AuthUserOrganizer.exec({email, password}).then((i) {
  console.log(i.context);
});
...
