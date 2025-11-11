-- 主数据库表结构（所有功能模块）
-- 请在 MySQL 中执行此 SQL 建表，注意调整 charset/collation 与权限。

CREATE TABLE IF NOT EXISTS users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(50) NOT NULL UNIQUE,
  password VARCHAR(255) NOT NULL,
  role ENUM('elder','child','caregiver','manager') NOT NULL,
  name VARCHAR(50),
  phone VARCHAR(20)
);

CREATE TABLE IF NOT EXISTS health_profiles (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT NOT NULL,
  birth DATE,
  gender ENUM('male','female'),
  disease_history TEXT,
  allergy TEXT,
  medication_plan TEXT,
  FOREIGN KEY(user_id) REFERENCES users(id),
  UNIQUE KEY uq_health_user (user_id)
);

CREATE TABLE IF NOT EXISTS medication_reminders (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT NOT NULL,
  medicine VARCHAR(100),
  dose VARCHAR(50),
  time DATETIME,
  taken BOOLEAN DEFAULT 0,
  FOREIGN KEY(user_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS service_orders (
  id INT AUTO_INCREMENT PRIMARY KEY,
  elder_id INT NOT NULL,
  service_type VARCHAR(50),
  address VARCHAR(255),
  order_time DATETIME,
  status ENUM('pending','confirmed','done','cancelled') DEFAULT 'pending',
  feedback TEXT,
  amount DECIMAL(10,2),
  paid BOOLEAN DEFAULT 0,
  FOREIGN KEY(elder_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS emergency_alerts (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT NOT NULL,
  alert_type VARCHAR(50),
  time DATETIME,
  location VARCHAR(100),
  resolved BOOLEAN DEFAULT 0,
  FOREIGN KEY(user_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS relatives (
  id INT AUTO_INCREMENT PRIMARY KEY,
  elder_id INT NOT NULL,
  child_id INT NOT NULL,
  relation VARCHAR(20),
  FOREIGN KEY(elder_id) REFERENCES users(id),
  FOREIGN KEY(child_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS activities (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  details TEXT,
  time DATETIME,
  location VARCHAR(255)
);

CREATE TABLE IF NOT EXISTS activity_participants (
  id INT AUTO_INCREMENT PRIMARY KEY,
  activity_id INT,
  user_id INT,
  FOREIGN KEY(activity_id) REFERENCES activities(id),
  FOREIGN KEY(user_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS albums (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT,
  img_url VARCHAR(255),
  upload_time DATETIME
);

CREATE TABLE IF NOT EXISTS iot_devices (
  id INT AUTO_INCREMENT PRIMARY KEY,
  elder_id INT NOT NULL,
  device_type VARCHAR(50),
  device_id VARCHAR(100),
  status TEXT,
  last_update DATETIME,
  FOREIGN KEY(elder_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS audit_logs (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT,
  action VARCHAR(100),
  detail TEXT,
  time DATETIME,
  FOREIGN KEY(user_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS community_posts (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT NOT NULL,
  title VARCHAR(128) NOT NULL,
  content TEXT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  topic VARCHAR(32),
  FOREIGN KEY(user_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS community_comments (
  id INT AUTO_INCREMENT PRIMARY KEY,
  post_id INT NOT NULL,
  user_id INT NOT NULL,
  content TEXT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY(post_id) REFERENCES community_posts(id),
  FOREIGN KEY(user_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS elder_locations (
  id INT AUTO_INCREMENT PRIMARY KEY,
  elder_id INT NOT NULL,
  lat DOUBLE NOT NULL,
  lng DOUBLE NOT NULL,
  update_time DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY(elder_id) REFERENCES users(id),
  UNIQUE KEY uq_location_elder (elder_id)
);

CREATE TABLE IF NOT EXISTS elder_tracks (
  id INT AUTO_INCREMENT PRIMARY KEY,
  elder_id INT NOT NULL,
  lat DOUBLE NOT NULL,
  lng DOUBLE NOT NULL,
  timestamp DATETIME NOT NULL,
  FOREIGN KEY(elder_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS elder_geofence (
  id INT AUTO_INCREMENT PRIMARY KEY,
  elder_id INT NOT NULL,
  center_lat DOUBLE NOT NULL,
  center_lng DOUBLE NOT NULL,
  radius DOUBLE NOT NULL,
  FOREIGN KEY(elder_id) REFERENCES users(id),
  UNIQUE KEY uq_geofence_elder (elder_id)
);

CREATE TABLE IF NOT EXISTS memberships (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT NOT NULL,
  type ENUM('monthly','yearly') NOT NULL,
  start_date DATETIME NOT NULL,
  end_date DATETIME NOT NULL,
  auto_renew BOOLEAN DEFAULT 0,
  FOREIGN KEY(user_id) REFERENCES users(id)
);

CREATE TABLE IF NOT EXISTS member_coupons (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT NOT NULL,
  coupon_code VARCHAR(20) NOT NULL,
  discount DECIMAL(5,2) NOT NULL,
  valid_until DATETIME NOT NULL,
  used BOOLEAN DEFAULT 0,
  FOREIGN KEY(user_id) REFERENCES users(id)
);
