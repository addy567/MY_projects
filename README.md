<?php
/**
 * Plugin Name: Daily Report
 * Plugin URI: https://www.yourpluginuri.com/
 * Description: Sends a daily report to the admin containing the number of posts created and comments received on that day.
 * Version: 1.0
 * Author: Your Name
 * Author URI: https://www.yourwebsite.com/
 * License: GPL2
 */

// Schedule daily report email
add_action( 'wp', 'schedule_daily_report_email' );

function schedule_daily_report_email() {
    if ( ! wp_next_scheduled( 'send_daily_report_email' ) ) {
        wp_schedule_event( strtotime( 'midnight' ), 'daily', 'send_daily_report_email' );
    }
}

// Send daily report email
add_action( 'send_daily_report_email', 'send_daily_report_email_to_admin' );

function send_daily_report_email_to_admin() {
    $post_count = wp_count_posts()->publish;
    $comment_count = wp_count_comments()->approved;
    $subject = 'Daily Report - ' . date( 'F j, Y' );
    $message = sprintf( 'Number of posts created: %d<br>Number of comments received: %d', $post_count, $comment_count );
    $headers = array( 'Content-Type: text/html; charset=UTF-8' );
    wp_mail( get_option( 'admin_email' ), $subject, $message, $headers );
}
