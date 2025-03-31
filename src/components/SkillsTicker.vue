<template>
    <div class="ticker">
        <div class="ticker-content">
            <div v-for="(skill, index) in repeatedSkills" :key="index" class="ticker-item">
                <img :src="skill.logo" :alt="skill.name" class="logo" />
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
    { name: "PostgresSQL", logo: "/logos/postgres.svg" },
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
    const repeatedSkills = computed(() => [...skills, ...skills, ...skills, ...skills, ...skills]);
</script>

<style scoped>
    .ticker {
        position: fixed;
        bottom: 0;
        width: 100%;
        height: 50px;
        background: rgba(0, 0, 0, 0.9);
        overflow: hidden;
        display: flex;
        align-items: center;
        white-space: nowrap;
        z-index: 5;
    }

    .ticker-content {
        display: inline-flex;
        animation: scroll 20s linear infinite;
        will-change: transform;
    }

    .ticker-item {
        display: flex;
        align-items: center;
        padding: 0 20px;
        flex-shrink: 0;
    }

    .logo {
        height: 30px;
        margin-right: 10px;
    }

    @keyframes scroll {
        0% {
            transform: translate3d(0, 0, 0);
        }
        100% {
            transform: translate3d(-20%, 0, 0);
        }
    }
</style>