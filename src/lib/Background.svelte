<script lang="ts">
    import { onMount } from "svelte";

    let height: number = 0;
    let width: number = 0;
    let canvas: HTMLCanvasElement;
    let ctx: CanvasRenderingContext2D | null = null;

    const rotateVelocity = (velocity: {x: number, y: number}, angle: number) => {
        const rotatedVelocities = {
            x: velocity.x * Math.cos(angle) - velocity.y * Math.sin(angle),
            y: velocity.x * Math.sin(angle) + velocity.y * Math.cos(angle)
        };
        return rotatedVelocities;
}

    class Body{
        private isColliding: boolean = false;
        private position: {
            x: number;
            y: number;
        }
        private radius: number;
        private color: string;
        private velocity: {
            x: number;
            y: number;
        } = {x: 0, y: 0};
        private mass: number;
        constructor(x: number, y: number, radius: number, mass: number, color: string){
            this.position = {x, y};
            this.radius = radius;
            this.color = color;
            this.mass = mass;
            const initialDir = Math.random() * Math.PI * 2;
            const initialVelocity = Math.random() * 2 + 1;
            this.velocity = {
                x: Math.cos(initialDir) * initialVelocity,
                y: Math.sin(initialDir) * initialVelocity
            }
        }
        public draw = () => {
            ctx!.beginPath();
            ctx!.arc(this.position.x, this.position.y, this.radius, 0, Math.PI * 2, false);
            ctx!.fillStyle = '#222';
            ctx!.fill();
            ctx!.strokeStyle = this.color;
            ctx!.stroke();
            ctx!.closePath();
        }

        public testCollision = (otherBody: Body) => {
          
            const dx = otherBody.position.x - this.position.x;
            const dy = otherBody.position.y - this.position.y;
            const distance = Math.sqrt(dx * dx + dy * dy);

            if(distance < this.radius + otherBody.radius + 20){
                const force = (this.mass * otherBody.mass) / (distance * distance) * 0.6;
                this.velocity.x += -dx * force; 
                this.velocity.y += -dy * force;
                if(distance < this.radius + otherBody.radius){
                    this.resolveCollision(otherBody);
                    this.isColliding = true
                }
                return true;
            }

            
            const force = (this.mass * otherBody.mass) / (distance * distance) * 0.1;
            this.velocity.x += dx * force; 
            this.velocity.y += dy * force;
            return false;
        }

        public resolveCollision = (otherBody: Body) => {
            const xVelocityDiff = this.velocity.x - otherBody.velocity.x;
            const yVelocityDiff = this.velocity.y - otherBody.velocity.y;

            const xDist = otherBody.position.x - this.position.x;
            const yDist = otherBody.position.y - this.position.y;

            if (xVelocityDiff * xDist + yVelocityDiff * yDist >= 0) {
                const angle = -Math.atan2(otherBody.position.y - this.position.y, otherBody.position.x - this.position.x);

                const m1 = this.mass;
                const m2 = otherBody.mass;

                const u1 = rotateVelocity(this.velocity, angle);
                const u2 = rotateVelocity(otherBody.velocity, angle);

                const v1 = { x: u1.x * (m1 - m2) / (m1 + m2) + u2.x * 2 * m2 / (m1 + m2), y: u1.y };
                const v2 = { x: u2.x * (m1 - m2) / (m1 + m2) + u1.x * 2 * m2 / (m1 + m2), y: u2.y };

                const vFinal1 = rotateVelocity(v1, -angle);
                const vFinal2 = rotateVelocity(v2, -angle);

                this.velocity.x = vFinal1.x;
                this.velocity.y = vFinal1.y;

                otherBody.velocity.x = vFinal2.x;
                otherBody.velocity.y = vFinal2.y;
            }

            this.velocity.x *= 0.8;
            this.velocity.y *= 0.8;
            otherBody.velocity.x *= 0.8;
            otherBody.velocity.y *= 0.8;
        }

        private applyMouseForce = (mousePos: {x: number, y: number}) => {
            const dx = mousePos.x - this.position.x;
            const dy = mousePos.y - this.position.y;
            const distance = Math.sqrt(dx * dx + dy * dy);
            const forceDirection = {
                x: -dx / distance,
                y: -dy / distance
            }
            const maxDistance = 100;
            const force = 4;
            const finalForce = distance > maxDistance ? 0 : force;
            this.velocity.x += forceDirection.x * finalForce;
            this.velocity.y += forceDirection.y * finalForce;
        }

        public update = () => {
            this.position.x += this.velocity.x;
            this.position.y += this.velocity.y;

            this.applyMouseForce(mousePos);

            if(this.isColliding) return this.isColliding = false;
            const dx = width / 2 - this.position.x;
            const dy = height / 2 - this.position.y;
            const distance = Math.sqrt(dx * dx + dy * dy);

            const force = Math.log10(distance) * 0.0001;
            const finalForce = force;
            
            this.velocity.x += dx * finalForce;
            this.velocity.y += dy * finalForce;

            
        }
    }

    const len = 200;
    const bodies: Body[] = [];

    const mousePos = {x: width / 2, y: height / 2};
    window.addEventListener('mousemove', (e) => {
        mousePos.x = e.clientX;
        mousePos.y = e.clientY;
    });

    const init = () => {
        const colors = ['#F3AA60', '#EF6262', '#468B97', '#1D5B79',];
        if(!canvas) return;
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        ctx = canvas.getContext('2d');
        for(let i = 0; i < len; i++) {
            const radius = Math.random() * 10 + 5;
            bodies.push(new Body(Math.random() * width, Math.random() * height, radius, 15 / radius, colors[Math.floor(Math.random() * colors.length)]));
        }
            
        simulate();
    }

    const simulate = () => {
        ctx!.clearRect(0, 0, width, height);
        for(let i = 0; i < bodies.length; i++){
            for(let j = i + 1; j < bodies.length; j++){
                bodies[i].testCollision(bodies[j]);
            }
            bodies[i].update();
            bodies[i].draw();
        }
        requestAnimationFrame(simulate);
    }

    onMount(init);

    $: if(width) canvas.width = width;
    $: if(height) canvas.height = height;
</script>

<main>
    <canvas bind:this={canvas} bind:clientHeight={height} bind:clientWidth={width}></canvas>
</main>

<style>
    canvas{
        background-color: var(--bg);
        position: absolute;
        height: 100vh;
        width: 100vw;
        left: 0; top: 0;
    }
</style>