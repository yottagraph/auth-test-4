<template>
    <div class="auth-page">
        <div class="auth-content">
            <div class="status-badge">
                <span class="status-dot"></span>
                <span class="status-label">AUTHENTICATED</span>
            </div>

            <div class="hero">
                <div class="check-ring">
                    <v-icon icon="mdi-check" size="44" color="#3FEA00" />
                </div>

                <h1 class="hero-title">Welcome{{ firstName ? `, ${firstName}` : '' }}.</h1>
                <p class="hero-subtitle">{{ appName }} signed you in successfully via Auth0.</p>
            </div>

            <div class="profile-card">
                <div class="profile-row">
                    <v-avatar size="56" color="surface" class="profile-avatar">
                        <img
                            v-if="avatarUrl && !avatarError"
                            :src="avatarUrl"
                            :alt="userName"
                            crossorigin="anonymous"
                            referrerpolicy="no-referrer"
                            @error="avatarError = true"
                            style="width: 100%; height: 100%; object-fit: cover"
                        />
                        <span v-else class="avatar-initials">{{ userInitials }}</span>
                    </v-avatar>
                    <div class="profile-meta">
                        <div class="profile-name">{{ userName || 'Signed in' }}</div>
                        <div class="profile-id">{{ userId || '—' }}</div>
                    </div>
                </div>
            </div>

            <div class="info-grid">
                <div class="info-tile">
                    <div class="info-label">Identity Provider</div>
                    <div class="info-value">Auth0</div>
                </div>
                <div class="info-tile">
                    <div class="info-label">Session</div>
                    <div class="info-value">
                        <span class="pulse-dot"></span>
                        Active
                    </div>
                </div>
                <div class="info-tile">
                    <div class="info-label">Access</div>
                    <div class="info-value">{{ permissionLabel }}</div>
                </div>
                <div class="info-tile">
                    <div class="info-label">Tenant</div>
                    <div class="info-value">{{ appName }}</div>
                </div>
            </div>

            <div class="footnote">
                <v-icon icon="mdi-shield-check-outline" size="16" class="mr-1" />
                End-to-end auth flow verified — login, callback, sealed cookie, session.
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { computed, ref } from 'vue';

    import { useProxiedAvatar } from '~/composables/useProxiedAvatar';
    import { useUserState } from '~/composables/useUserState';

    const { appName } = useAppInfo();
    const { userName, userId, userPicture, userIsPermitted } = useUserState();

    const { proxiedUrl: avatarUrl } = useProxiedAvatar(userPicture);
    const avatarError = ref(false);

    const firstName = computed(() => {
        if (!userName.value) return '';
        return userName.value.split(' ')[0];
    });

    const userInitials = computed(() => {
        const name = userName.value;
        if (!name) return '?';
        const parts = name.split(' ').filter(Boolean);
        if (parts.length >= 2) {
            return (parts[0][0] + parts[parts.length - 1][0]).toUpperCase();
        }
        return name.substring(0, 2).toUpperCase();
    });

    const permissionLabel = computed(() => (userIsPermitted() ? 'read:all' : 'limited'));
</script>

<style scoped>
    .auth-page {
        min-height: 100%;
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        padding: 48px 24px;
        background:
            radial-gradient(ellipse at top, rgba(63, 234, 0, 0.06) 0%, rgba(10, 10, 10, 0) 55%),
            radial-gradient(
                ellipse at bottom right,
                rgba(0, 59, 255, 0.05) 0%,
                rgba(10, 10, 10, 0) 60%
            ),
            var(--lv-black);
    }

    .auth-content {
        width: 100%;
        max-width: 620px;
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 36px;
    }

    .status-badge {
        display: inline-flex;
        align-items: center;
        gap: 10px;
        padding: 6px 14px;
        border-radius: 999px;
        background: rgba(63, 234, 0, 0.08);
        border: 1px solid rgba(63, 234, 0, 0.25);
    }

    .status-dot {
        width: 6px;
        height: 6px;
        border-radius: 50%;
        background: var(--lv-green);
        box-shadow: 0 0 8px rgba(63, 234, 0, 0.8);
    }

    .status-label {
        font-family: var(--font-mono);
        font-size: 0.7rem;
        letter-spacing: 0.18em;
        color: var(--lv-green);
    }

    .hero {
        text-align: center;
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 16px;
    }

    .check-ring {
        width: 84px;
        height: 84px;
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        background: rgba(63, 234, 0, 0.08);
        border: 1px solid rgba(63, 234, 0, 0.4);
        position: relative;
        animation: ringIn 600ms cubic-bezier(0.16, 1, 0.3, 1) both;
    }

    .check-ring::before {
        content: '';
        position: absolute;
        inset: -6px;
        border-radius: 50%;
        border: 1px solid rgba(63, 234, 0, 0.18);
        animation: ringPulse 3s ease-in-out infinite;
    }

    .hero-title {
        font-family: var(--font-headline);
        font-weight: 400;
        font-size: 2.25rem;
        letter-spacing: 0.01em;
        color: var(--lv-white);
        margin: 0;
        line-height: 1.15;
    }

    .hero-subtitle {
        color: var(--lv-silver);
        font-size: 1.025rem;
        margin: 0;
        max-width: 460px;
        line-height: 1.5;
    }

    .profile-card {
        width: 100%;
        padding: 18px 22px;
        background: var(--lv-surface);
        border: 1px solid rgba(255, 255, 255, 0.06);
        border-radius: 14px;
    }

    .profile-row {
        display: flex;
        align-items: center;
        gap: 16px;
    }

    .profile-avatar {
        border: 1px solid rgba(255, 255, 255, 0.08);
    }

    .avatar-initials {
        font-family: var(--font-mono);
        font-size: 1.1rem;
        color: var(--lv-green);
        letter-spacing: 0.05em;
    }

    .profile-meta {
        display: flex;
        flex-direction: column;
        gap: 4px;
        min-width: 0;
        overflow: hidden;
    }

    .profile-name {
        font-family: var(--font-headline);
        font-size: 1.05rem;
        color: var(--lv-white);
        letter-spacing: 0.01em;
    }

    .profile-id {
        font-family: var(--font-mono);
        font-size: 0.78rem;
        color: var(--lv-silver);
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
    }

    .info-grid {
        width: 100%;
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        gap: 12px;
    }

    .info-tile {
        padding: 14px 16px;
        background: rgba(20, 20, 20, 0.7);
        border: 1px solid rgba(255, 255, 255, 0.05);
        border-radius: 10px;
        display: flex;
        flex-direction: column;
        gap: 6px;
        transition:
            border-color 200ms ease,
            background 200ms ease;
    }

    .info-tile:hover {
        border-color: rgba(63, 234, 0, 0.2);
        background: rgba(20, 20, 20, 1);
    }

    .info-label {
        font-family: var(--font-mono);
        font-size: 0.68rem;
        letter-spacing: 0.14em;
        color: var(--lv-silver);
        text-transform: uppercase;
    }

    .info-value {
        font-family: var(--font-headline);
        font-size: 0.95rem;
        color: var(--lv-white);
        display: flex;
        align-items: center;
        gap: 8px;
    }

    .pulse-dot {
        width: 7px;
        height: 7px;
        border-radius: 50%;
        background: var(--lv-green);
        box-shadow: 0 0 0 0 rgba(63, 234, 0, 0.7);
        animation: pulse 1.8s ease-out infinite;
    }

    .footnote {
        display: inline-flex;
        align-items: center;
        font-family: var(--font-mono);
        font-size: 0.78rem;
        color: var(--lv-silver);
        letter-spacing: 0.03em;
    }

    @keyframes ringIn {
        from {
            transform: scale(0.6);
            opacity: 0;
        }
        to {
            transform: scale(1);
            opacity: 1;
        }
    }

    @keyframes ringPulse {
        0%,
        100% {
            transform: scale(1);
            opacity: 0.6;
        }
        50% {
            transform: scale(1.08);
            opacity: 0.2;
        }
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(63, 234, 0, 0.55);
        }
        70% {
            box-shadow: 0 0 0 8px rgba(63, 234, 0, 0);
        }
        100% {
            box-shadow: 0 0 0 0 rgba(63, 234, 0, 0);
        }
    }

    @media (max-width: 600px) {
        .info-grid {
            grid-template-columns: 1fr;
        }
        .hero-title {
            font-size: 1.85rem;
        }
    }
</style>
