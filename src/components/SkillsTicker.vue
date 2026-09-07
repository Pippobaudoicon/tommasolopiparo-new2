<template>
    <div class="ticker" role="region" aria-label="Technologies I work with" tabindex="0">
        <div class="ticker-content">
            <div v-for="(skill, index) in repeatedSkills" :key="index" class="ticker-item" :aria-hidden="index >= skills.length ? true : undefined">
                <img :src="skill.logo" alt="" class="logo" />
                <span>{{ skill.name }}</span>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { computed } from "vue";

    const skills = [
    { name: "JavaScript", logo: "/logos/javascript.svg" },
    { name: "TypeScript", logo: "/logos/typescript.svg" },
    { name: "PHP", logo: "/logos/php.svg" },
    { name: "Node.js", logo: "/logos/nodejs.svg" },
    { name: "Docker", logo: "/logos/docker.svg" },
    { name: "Kubernetes", logo: "/logos/kubernetes.svg"},
    { name: "MongoDB", logo: "/logos/mongodb.svg" },
    { name: "MySQL", logo: "/logos/mysql.svg" },
    { name: "PostgreSQL", logo: "/logos/postgres.svg" },
    { name: "Git", logo: "/logos/git.svg"},
    { name: "Laravel", logo: "/logos/laravel.svg"},
    { name: "Redis", logo: "/logos/redis.svg"},
    { name: "Elasticsearch", logo: "/logos/elasticsearch.svg"},
    { name: "RabbitMQ", logo: "/logos/rabbitmq.svg"},
    { name: "AWS", logo: "/logos/aws.svg"},
    { name: "Linux", logo: "/logos/linux.svg"},
    { name: "Nginx", logo: "/logos/nginx.svg"},
    ];

    // Repeat skills to ensure seamless scrolling
    const repeatedSkills = computed(() => [...skills, ...skills]);
</script>

<style scoped>
    .ticker {
        position: relative;
        width: 100%;
        height: 56px;
        flex-shrink: 0;
        background: #080b11;
        border-top: 1px solid #a9c1e210;
        overflow: hidden;
        display: flex;
        align-items: center;
        white-space: nowrap;
        z-index: 5;
        mask-image: linear-gradient(to right, transparent, #000 8%, #000 92%, transparent);
    }

    .ticker-content {
        display: inline-flex;
        animation: scroll 55s linear infinite;
        will-change: transform;
    }

    .ticker-item {
        display: flex;
        align-items: center;
        padding: 0 25px;
        flex-shrink: 0;
        font-size: 11px;
        letter-spacing: 0.02em;
        color: #a1aabd;
        transition: color 200ms;
    }

    .logo {
        height: 19px;
        width: 24px;
        object-fit: contain;
        margin-right: 9px;
        filter: grayscale(0.8);
        opacity: 0.75;
        transition: filter 200ms, opacity 200ms;
    }

    .ticker:is(:hover, :focus) .ticker-content { animation-play-state: paused; }

    .ticker:is(:hover, :focus-visible) .logo {
        filter: none;
        opacity: 1;
    }

    .ticker:is(:hover, :focus-visible) .ticker-item { color: #e9edf4; }

    @media (prefers-reduced-motion: reduce) {
        .ticker { overflow-x: auto; }
        .ticker-item[aria-hidden="true"] { display: none; }
    }

    @keyframes scroll {
        0% {
            transform: translate3d(0, 0, 0);
        }
        100% {
            transform: translate3d(-50%, 0, 0);
        }
    }
</style>
