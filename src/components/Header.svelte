<header>
    <div class="header-container">
        <img src="/images/logo-full.svg" alt="Logo" />
        <nav>
            <ul class="navigation">
                <li>
                    <a href="https://github.com/shinjl/xforecast" title="Github"
                        ><img class="header-link" src="/images/github.svg" alt="GitHub" /></a
                    >
                </li>
                <li>
                    <a href="mailto:info@shinjl.com" title="E-Mail"
                        ><img class="header-link" src="/images/mail.svg" alt="E-Mail" /></a
                    >
                </li>
            </ul>
        </nav>
    </div>
</header>

<style>
    header {
        background: #f65751;
        padding: 1rem 0;
        margin-bottom: 1.5rem;
    }

    .header-container {
        margin: 0 auto;
        max-width: 1200px;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    img {
        height: 60px;
    }

    .navigation {
        display: flex;
        list-style: none;
        justify-content: space-between;
        align-items: center;
        align-content: center;
    }

    .navigation li {
        padding: 0 0 0 20px;
    }

    .header-link {
        width: 32px;
        height: 32px;
    }
</style>
