# 24bda90001-fullstack-24bds-4b-exp1.3.2
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RBAC Security Implementation</title>
    <style>
        :root {
            --primary: #2563EB;
            --bg: #F1F5F9;
            --text: #0F172A;
            --danger: #DC2626;
            --success: #16A34A;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
        }
        .container {
            background: white;
            padding: 2rem;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
            width: 100%;
            max-width: 600px;
        }
        h2 {
            text-align: center;
            border-bottom: 2px solid #E2E8F0;
            padding-bottom: 10px;
        }
        .form-group { margin-bottom: 1rem; }
        label {
            font-weight: bold;
            display: block;
            margin-bottom: 0.5rem;
        }
        select, button {
            width: 100%;
            padding: 0.75rem;
            border-radius: 6px;
            border: 1px solid #CBD5E1;
            font-size: 1rem;
            box-sizing: border-box;
        }
        button {
            background-color: var(--primary);
            color: white;
            border: none;
            font-weight: bold;
            cursor: pointer;
            transition: 0.2s;
        }
        button:hover { opacity: 0.9; }

        #app-interface { display: none; }

        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            background: #F8FAFC;
            padding: 15px;
            border-radius: 8px;
            border: 1px solid #E2E8F0;
        }

        .role-badge {
            padding: 5px 10px;
            border-radius: 20px;
            font-size: 0.85rem;
            font-weight: bold;
            color: white;
            text-transform: uppercase;
        }

        .badge-student { background: var(--primary); }
        .badge-admin { background: var(--danger); }

        .nav-tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }

        .nav-tab {
            flex: 1;
            padding: 10px;
            text-align: center;
            background: #E2E8F0;
            border-radius: 6px;
            cursor: pointer;
            font-weight: bold;
        }

        .nav-tab.active {
            background: var(--primary);
            color: white;
        }

        .route-view {
            display: none;
            padding: 20px;
            border: 1px solid #E2E8F0;
            border-radius: 8px;
        }

        .route-view.active { display: block; }

        .action-btn {
            margin-top: 10px;
            padding: 10px;
            width: auto;
            display: inline-block;
        }

        .btn-danger { background: var(--danger); }
        .btn-success { background: var(--success); }

        .error-message {
            color: var(--danger);
            font-weight: bold;
            background: #FEE2E2;
            padding: 10px;
            border-radius: 5px;
            margin-top: 15px;
            text-align: center;
            display: none;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>🛡️ Role-Based Access Control (RBAC)</h2>

    <div id="login-section">
        <p style="text-align: center; color: #64748B;">
            Select a role profile to authenticate into the Data Science Portal.
        </p>

        <div class="form-group">
            <label>Select User Role</label>
            <select id="user-role">
                <option value="student">2nd-Year Student (Read/Write Code)</option>
                <option value="admin">Database Administrator (Full Access)</option>
            </select>
        </div>

        <button onclick="login()">Authenticate & Load Permissions</button>
    </div>

    <div id="app-interface">
        <div class="header">
            <div>
                <strong>Active Session:</strong><br>
                <span id="role-display"></span>
            </div>

            <button onclick="logout()"
                    style="width: auto; background: #64748B; padding: 8px 15px;">
                Logout
            </button>
        </div>

        <div class="nav-tabs">
            <div class="nav-tab active"
                 onclick="navigate('public-route', 'public', this)">
                Public Syllabus
            </div>

            <div class="nav-tab"
                 onclick="navigate('sql-route', 'student', this)">
                SQL Workspace
            </div>

            <div class="nav-tab"
                 onclick="navigate('admin-route', 'admin', this)">
                DB Admin Panel
            </div>
        </div>

        <div id="access-denied" class="error-message">
            ⛔ 403 Forbidden: Your current role does not have permission to access this route.
        </div>

        <div id="public-route" class="route-view active">
            <h3>📖 Data Science Curriculum</h3>
            <p><strong>Required Permissions:</strong> None (Public)</p>
            <p>
                Welcome to the curriculum page. Here you can find the syllabus
                for Business Intelligence, Python programming, and Database Management.
            </p>
            <button class="action-btn">Download Syllabus</button>
        </div>

        <div id="sql-route" class="route-view">
            <h3>💻 SQL Practice Environment</h3>
            <p>
                <strong>Required Permissions:</strong>
                <code>student</code> or <code>admin</code>
            </p>

            <p>
                Run your <code>SELECT</code>, <code>INSERT</code>, and
                <code>UPDATE</code> syntax queries here to practice for your upcoming vivas.
            </p>

            <div style="background: #1E293B; color: #38BDF8; padding: 15px;
                        border-radius: 5px; font-family: monospace; margin-bottom: 10px;">
                SELECT * FROM insurance_claims WHERE status = 'pending';
            </div>

            <button class="action-btn btn-success">Execute Query</button>
        </div>

        <div id="admin-route" class="route-view">
            <h3>⚙️ Database Administration</h3>

            <p>
                <strong>Required Permissions:</strong>
                <code>admin</code> strictly
            </p>

            <p>
                Warning: Actions taken here affect the production database.
                Only authorized database administrators can validate data quality
                or drop tables.
            </p>

            <button class="action-btn btn-danger">DROP TABLE users;</button>
            <button class="action-btn" style="background: #D97706;">
                Override Viva Grades
            </button>
        </div>
    </div>
</div>

<script>
    const rolePermissions = {
        student: ['public', 'student'],
        admin: ['public', 'student', 'admin']
    };

    let currentUserRole = null;

    function login() {
        currentUserRole = document.getElementById('user-role').value;

        document.getElementById('login-section').style.display = 'none';
        document.getElementById('app-interface').style.display = 'block';

        const badgeClass =
            currentUserRole === 'admin'
                ? 'badge-admin'
                : 'badge-student';

        document.getElementById('role-display').innerHTML =
            `<span class="role-badge ${badgeClass}">${currentUserRole}</span>`;

        navigate('public-route', 'public',
            document.querySelector('.nav-tab'));
    }

    function logout() {
        currentUserRole = null;

        document.getElementById('login-section').style.display = 'block';
        document.getElementById('app-interface').style.display = 'none';
    }

    function navigate(routeId, requiredPermissionLevel, clickedTab) {
        document.querySelectorAll('.route-view')
            .forEach(el => el.classList.remove('active'));

        document.querySelectorAll('.nav-tab')
            .forEach(el => el.classList.remove('active'));

        document.getElementById('access-denied').style.display = 'none';

        if (clickedTab) {
            clickedTab.classList.add('active');
        }

        const userAllowedPermissions =
            rolePermissions[currentUserRole];

        if (userAllowedPermissions &&
            userAllowedPermissions.includes(requiredPermissionLevel)) {

            document.getElementById(routeId).classList.add('active');

        } else {
            document.getElementById('access-denied').style.display = 'block';
        }
    }
</script>

</body>
</html>
