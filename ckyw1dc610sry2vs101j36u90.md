## Top 6 React State Management libraries for 2022

One of the most critical parts of any app is state management. What users view, how the app looks, what data is kept, and so on are all determined by the app's state. It's no surprise, then, that there are so many open-source libraries dedicated to making state management simpler and more pleasurable.

React is undoubtedly the one with the most vibrant ecosystem, including state management libraries, among the several JavaScript UI frameworks. This blog will examine the top six of these libraries to see which one is the greatest fit for your next React app.

# 1. Recoil


![1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643228260318/PIpyaY6B0.png)

Recoil has piqued the interest of the React community since its announcement in early 2020. Partly due to its atomic, React-inspired approach to state management, but partly due to the fact that it was developed by the Facebook team. Recoil is relatively reliable and feature-rich, despite the fact that it is still in the "experimental" phase.

Atoms and selectors are the most important things to grasp in Recoil. Single state property is wrapped and represented by an atom, which is a unit of state. It's similar to React's local state (useState), but with the added benefit of being able to be shared across components and created outside of them.

```
const isAvailableState = atom({
  key: "isAvailableState", // unique, required key
  default: true, // default value
});
```
Selectors, on the other hand, are pure functions that rely on atoms or other selectors to calculate their value, and they recalculate when any of their dependents change. They're readable, writable, and even async, so they're perfect for React Suspense.

```
const statusState = selector({
  key: "statusState", // unique, required key
  get: ({ get }) => {
    const isAvailable = get(isAvailableState); // access value of the atom

    return isAvailable ? "Available" : "Unavailable";
  },
});
```
You'll need to use one of Recoil's hooks, such as useRecoilValue or useRecoilState, to use atoms and selectors.

```
// Inside React component

// state's value and state setter
const [isAvailable, setIsAvailable] = useRecoilState(isAvailableState);
// state's value only
const status = useRecoilValue(statusState);
```

Recoil uses atoms and selectors to provide comprehensive capabilities for all aspect of state management, from code to development tools to testing. Recoil is simple to use for beginners yet powerful enough for advanced users thanks to this technique.


# 2. Redux


![2.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643228637860/TRi16PLsf.png)

Without Redux, no list of React state management libraries would be complete. Although Redux has recently received a lot of criticism, it's still a strong, battle-tested library that can work alongside modern solutions.

You can only change the state of Redux by dispatching an action, which is an object that describes what should happen. This, in turn, calls a reducer, which returns a new state given an action object and a prior state.

While the Redux paradigm has been around for a while, the boilerplate that comes with it is still a problem. Writing actions to represent every potential state change, followed by many reducers to handle those actions, can soon result in a large amount of code that is difficult to manage. It was for this reason that Redux Toolkit was established.

Nowadays, Redux Toolkit is the preferred method of implementing Redux. It streamlines the shop setup process, decreases the amount of boilerplate required, and adheres to best practices by default. It also includes batteries, as well as apps like Immer, which allows for quick state updates, and Redux-Thunk, which works with async logic.


```
const exampleSlice = createSlice({
  name: "example",
  initialState: {
    isAvailable: true,
  },
  reducers: {
    makeAvailable: (state) => {
      state.isAvailable = true;
    },
    makeUnavailable(state) {
      state.isAvailable = false;
    },
  },
});
const { makeAvailable, makeUnavailable } = exampleSlice.actions;
const exampleReducer = exampleSlice.reducer;
const store = configureStore({
  reducer: { example: exampleReducer },
});

// Inside React components with React-Redux hooks
const isAvailable = useSelector((state) => state.example.isAvailable);
const dispatch = useDispatch();

dispatch(makeAvailable());
dispatch(makeUnavailable());
```

# 3. Jotai


![3.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643228675522/eE324CMUc.png)

Jotai is another library worth examining if you're interested in atomic state management. It's comparable to Recoil, but smaller (3.2 kB vs. 21.1 kB), with a more simple API, better TypeScript support, more documentation, and no experimental label!

Apart from all of the foregoing, the most notable distinction between Recoil and Jotai is most likely their performance, particularly their waste collection. Recoil tracks its state using string keys, as you may have noticed from the preceding excerpts. This is why string keys are essential and must be unique. It's inconvenient, ineffective, and can cause memory loss.

Jotai, on the other hand, does not require keys and relies solely on JavaScript's built-in WeakMap to keep track of its atoms. This means that the JS engine takes care of garbage collection automatically, resulting in better memory use and performance.

Jotai simplifies its key notions, even more, implying that everything is an atom here! The previous Recoil snippets were applied to Jotai as follows:

```
const isAvailableState = atom(true);
const statusState = atom(({ get }) => {
  const isAvailable = get(isAvailableState); // access value of the atom

  return isAvailable ? "Available" : "Unavailable";
});
// Inside React component

// state's value and state setter
const [isAvailable, setIsAvailable] = useAtom(isAvailableState);
// ignoring state setter
const [status] = useAtom(statusState);
```
Jotai has nearly all of the functionality found in Recoil, as well as similar ease of integration with React Suspense and other React ecosystem products like Redux and Zustand.

# 4. Rematch

![4.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643228842321/6J4STWmVC.png)


If you want to stay in the Redux realm a bit longer, there's a Redux Toolkit option worth noting. Rematch is a lighter, faster, and easier-to-use alternative to Redux Toolkit.

Rematch extends Redux core by making the setup process easier, removing boilerplate, and integrating async/await for simplified side-effects management. All of this (as well as a plugin system and excellent TypeScript compatibility) is contained in just 1.7 kB. (vs. 11.1 kB of Redux Toolkit).

Models, which bundle state, reducers, and effects into a single object, are at the heart of Rematch. They make state management easier by enforcing Redux's recommended practices.

```
const countModel = {
  state: 0,
  reducers: {
    increment(state, payload) {
      return state + payload;
    },
  },
  effects: (dispatch) => ({
    async incrementAsync(payload) {
      await new Promise((resolve) => setTimeout(resolve, 1000));
      dispatch.count.increment(payload);
    },
  }),
};
```
The model may then be used to build a Redux store using Rematch features like shortcut action dispatchers.

```
const store = init({
  models: {
    count: countModel,
  },
});
const { dispatch } = store;
dispatch({ type: "count/increment", payload: 1 });
dispatch.count.increment(1); // Action shortcut
dispatch({ type: "count/incrementAsync", payload: 1 });
dispatch.count.incrementAsync(1); // Async action shortcut
```

# 5. Zutstand


![5.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643229043221/HZ_Gz8WFo.png)

Zustand is the smallest state management library on this list, weighing in at around 1 kB. That said, don't be fooled by the small size; Zustand's simple, minimalistic API can do a lot when utilised correctly.

Although notions such as actions and selectors exist in Zustand, hooks play the most important function. The create method of a Zustand store, for example, returns a hook that may be used in React components.

```
const useStore = create((set, get) => ({
  isAvailable: true,
  status: () =>
    get((state) => (get().isAvailable ? "Available" : "Unavailable")),
  makeAvailable: () => set((state) => ({ ...state, isAvailable: true })),
  makeUnavailable: () => set((state) => ({ ...state, isAvailable: false })),
}));

// Inside React component
const { isAvailable, makeAvailable, makeUnavailable } = useStore(); // Access whole store
const status = useStore((state) => state.status); // Access selected property
```
Zustand addresses typical challenges in React state management, such as usage in React concurrent mode or transitory state updates, in addition to its ease of use and compact size (without causing re-renders).


# 6. Hookstate


![6.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643229564836/O0POt4h1wO.png)

Hookstate is one of the many hook-centric libraries available. This library focuses on hooks and functional components to provide a great development experience.

Hookstate's API and ease of use are without a doubt its most appealing features. You can use the same methods with the same syntax whether you're working with a global or local state. Hookstate is thus not only a global state management library, but also a supercharged version of useState.

```
const state = createState({
  isAvailable: true,
});
// Wrapped "actions"
const makeAvailable = () => state.isAvailable.set(true); // Changing state outside component
const makeUnavailable = () => state.isAvailable.set(false);
const status = () => (state.isAvailable.get() ? "Available" : "Unavailable"); // Accessing state outside component

// Inside React component
const state = useState();
const isAvailable = state.isAvailable.get(); // Access selected property
```

Furthermore, because of its hierarchical state handling, Hookstate is particularly scalable. Because of mutations, scoped states, and other optimizations, you can use nested data structures with little to no performance cost using this module.

Hookstate appears to be one of the most appealing alternatives for React hooks aficionados, with a good extension system, TypeScript, and Redux DevTools support.


# Special Addition -> React Context

Although it's tempting to use a dedicated library - especially the most up-to-date one - it's important to remember that it's not always essential. Adding a new dependency to a tiny or medium app, which means greater bundle size, complexity, concepts, and so on, can do more harm than good.

React offers built-in capabilities for such situations, especially the State and Context APIs. For the great majority of apps, those will suffice.

It's wise to stick with the fundamentals until you need something more powerful. Then look into the various options, which range from lightweight options like Zustand to those with the most features and best suit the job, such as Redux or Recoil.

# Conclusion

So there you have it: this year's React state management frameworks to consider. Naturally, there are many more libraries like these out there, and new ones appear to be appearing on a daily basis. As a result, this list is likely to alter within the next year, if not sooner. The best option is to stick with what works while keeping an eye on what's out there in case something better comes along.




