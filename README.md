# Cooperative management

## Setup

Install the dependencies
```
npm install
```

Once the setup is completed you can simply run
```
npm run dev
```

### Known issues:
- The whole contract state is being read into the aepp. This is implemented as a workaround for: https://github.com/aeternity/hackathon-prague/issues/8
- State not being updated after transactions

### Todo:
- Implement blocking loaders
- Solve the issues
- Vuex for state management
- Change the CSS framework
- Implement component abstractions and routing
- Implement contract deployment via the client
- Replace hard coded contract with option to select a contract (or even better use AENS)
