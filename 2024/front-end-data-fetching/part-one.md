# Front-End Data Fetching Part One

## Scope

In this mini-series, we will progress through a testable, repeatable way of data fetching for a front-end application. This series applies to React and Vue, due to those frameworks not having built-in dependency injection. While there are lessons to be learned for Angular applications as well, not everything in here applies to Angular.

## The Current Problem

Here is the component we will be slowly improving over time in this series. It is simple on purpose.

```ts
export const MyComponent: React.FC = () => {
  const [data, setData] = useState<Array<string>>([]);

  useEffect(() => {
    fetch("https://www.myApi.com")
      .then((response) => response.json())
      .then((data: Array<string>) => {
        setData(data);
      });
  }, []);

  return (
    <div>
      {data.map(
        (item): React.JSX.Element => (
          <p key={item}>{item}</p>
        )
      )}
    </div>
  );
};
```

The most glaring issue with this component is that we are unable to test this component properly. This is due to the data fetching and rendering being done in the same component. We need to separate the data fetching from the renders. This means a second component is required.

By separating the component into two parts, we can employ the React/Vue method of dependency injection-passing properties.

## Conclusion

In the first part of this series, we employed the Container pattern. Allowing us to allocate one to fetching data, and the other to rendering data. This small change opened up the ability to test our rendering component.
