<script>
  const pi = Math.PI
  const sqrt = Math.sqrt
  const round = Math.round

  const norm = (u) => sqrt(u.x * u.x + u.y * u.y)
  const add = (u, v) => ({x: u.x + v.x, y: u.y + v.y})
  const smul = (s, u) => ({x: s * u.x, y: s * u.y})
  const neg = (u) => ({x: -u.x, y: -y.y})
  const rot = (a, u) => {
    const s = Math.sin(a)
    const c = Math.cos(a)
    return {x: c * u.x - s * u.y, y: s * u.x + c * u.y}
  }

  const size = 800
  const depth = 10
  const scale = 0.7
  const angle = pi / 7
  const center = {x: size / 2, y: size / 2}
  const length = 0.15 * size / 2 / 2
  const stroke_start = 8
  const stroke_scale = 0.8
  const total_length = length / (1 - scale)

  const d0 = {x: size/5, y: 0}
  const d1 = {x: 0, y: size/5}
  const centers = [center, add(center, d0), add(center, d1)]

  function t_color(t) {
    const start = [0, 0, 255];    // Blue
    const end = [255, 105, 180];  // Hot pink
    const r = round(start[0] + (end[0] - start[0]) * t);
    const g = round(start[1] + (end[1] - start[1]) * t);
    const b = round(start[2] + (end[2] - start[2]) * t);
    return `rgb(${r}, ${g}, ${b})`;
  }

  function branching() {
    let edges = []
    function loop(u, d, t, depth, stroke) {
      const v = add(u, d)
      const t1 = t + norm(d) / total_length
      edges.push({x0: u, x1: v, t0: t, t1: t1, stroke: stroke})
      if (depth >= 1)
        for (const s of [-1.2, 0.4])
          loop(v, smul(scale, rot(s*angle, d)), t1, depth-1, stroke_scale*stroke)
    }
    for (const c of centers)
      for (const k of [0, 1, 2])
        loop(c, rot(2 * pi / 3 * k + 0.001, {x: length, y: 0}), 0, depth, stroke_start)
    return edges
  }

  $: edges = branching()
</script>

<main>
	<h1>Hello Mae!!</h1>
  <svg width={size} height={size}>
    <defs>
      {#each edges as e, i}
        <linearGradient id={`gradient-${i}`} x1={e.x0.x} y1={e.x0.y} x2={e.x1.x} y2={e.x1.y}>
          <stop offset="0%" stop-color={t_color(e.t0)} />
          <stop offset="100%" stop-color={t_color(e.t1)} />
        </linearGradient>
      {/each}
    </defs>
    {#each edges as e, i}
      <line
        x1={e.x0.x} y1={e.x0.y} x2={e.x1.x} y2={e.x1.y}
        stroke={`url(#gradient-${i})`}
        stroke-width={e.stroke}
      />
    {/each}
  </svg>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
