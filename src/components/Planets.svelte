<script>
  import * as d3 from 'd3'
  import { isBrowser } from '../utils'

  if (isBrowser) {
    const planetContainer = d3.select('#planets')
    const theVacuumOfSpace = d3.select('#stars')

    function rando(min, max) {
      return Math.random() * (max - min) + min;
    }

    d3.json('data/planets.json')
      .then(data => {
        const svg = planetContainer.append('svg')
        svg.attr('height', 600)
        svg.attr('width', 600)

        const planets = svg.selectAll('circle').data(data)

        planets.attr('cy', 200)
          .attr('cx', d => d.distance)
          .attr('r', d => d.radius)
          .attr('fill', d => d.fill)

        planets.enter()
          .append('circle')
          .attr('cy', 200)
          .attr('cx', d => d.distance)
          .attr('r', d => d.radius)
          .attr('fill', d => d.fill)
      })
      .catch(err => console.error(err))

    d3.json('data/stars.json')
      .then(data => {

        const svg = theVacuumOfSpace.append('svg')
        svg.attr('height', 600)
        svg.attr('width', 600)

        const stars = svg.selectAll('circle').data(data)

        stars.attr('cy', 200)
          .attr('cx', d => d.distance)
          .attr('r', d => d.radius)
          .attr('fill', d => d.fill)



        for (let i = 0; i < data.length; i++) {
          stars.enter()
            .append('circle')
            .attr('cy', rando(i * i, 600))
            .attr('cx', rando(i * i, 600))
            .attr('r', d => d.radius)
            .attr('fill', d => d.fill)
        }
      })
      .catch(err => console.error(err))
  }
</script>

<style>
  #planets {
    position: absolute;
  }

</style>

<div id="planets"></div>
<div id="stars"></div>
