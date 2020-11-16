```js
const COLOR_ON = '#27efb5'; // cyan
const COLOR_OFF = '#efb527'; // orange

const useEnergyFlowEntering = (energy, { element }) => {
  anime({
    targets: element.current,
    backgroundColor: [COLOR_OFF, COLOR_ON],
    duration: energy.getDuration().enter,
    easing: 'linear'
  });
}

const useEnergyFlowExiting = (energy, { element }) => {
  anime({
    targets: element.current,
    backgroundColor: [COLOR_ON, COLOR_OFF],
    duration: energy.getDuration().exit,
    easing: 'linear'
  });
}

const useEnergyFlowRemove = (energy, { element }) => {
  anime.remove(element.current);
};

// Internal implementation
function useEnergyFlow (energy, refs) {
  useEnergyFlowEntering && useEnergyFlowEntering(energy)(refs);
  useEnergyFlowExiting && useEnergyFlowExiting(energy)(refs);
  useEnergyFlowRemove && useEnergyFlowRemove(energy)(refs);
}

function ItemComponent ({ energy, children }) {
  const element = React.useRef();

  energy.useEnergyFlow({ element });

  return (
    <div
      ref={element}
      style={{ padding: 5, backgroundColor: COLOR_OFF }}
    >
      <div>{energy.flow.value}</div>
      <div style={{ marginLeft: 10 }}>{children}</div>
    </div>
  );
}

const Item = withEnergy({
  useEnergyFlowEntering,
  useEnergyFlowExiting,
  useEnergyFlowRemove
})(ItemComponent);

function Sandbox () {
  const timeout = React.useRef();
  const [activate, setActivate] = React.useState(true);

  React.useEffect(() => {
    timeout.current = setTimeout(
      () => setActivate(!activate),
      2500
    );
    return () => clearTimeout(timeout.current);
  }, [activate]);

  return (
    <AnimationProvider duration={500}>
      <Item energy={{ activate }}>
        <Item>
          <Item />
        </Item>
      </Item>
    </AnimationProvider>
  );
}

render(<Sandbox />);
```
