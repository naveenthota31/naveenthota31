package com.example.naveen;

import android.os.Bundle;
import android.os.SystemClock;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class MainActivity extends AppCompatActivity {

    private Button startButton, stopButton;

    private boolean timerRunning;
    private long startTime;

    private Map<String, Long> tasksMap;
    private List<String> tasksList;
    private ArrayAdapter<String> tasksAdapter;
    private TextView timerTextView;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        timerTextView  = findViewById(R.id.timerTextView);
        startButton = findViewById(R.id.startButton);
        stopButton = findViewById(R.id.stopButton);
        ListView tasksListView = findViewById(R.id.tasksListView);

        timerRunning = false;
        tasksMap = new HashMap<>();
        tasksList = new ArrayList<>();
        tasksAdapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1, tasksList);
        tasksListView.setAdapter(tasksAdapter);

        startButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startTimer();
            }
        });

        stopButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                stopTimer();
            }
        });
    }

    private void startTimer() {
        if (!timerRunning) {
            startTime = SystemClock.elapsedRealtime();
            timerRunning = true;
            startButton.setEnabled(false);
            stopButton.setEnabled(true);
            updateTimer();
        }
    }


    private void stopTimer() {
        if (timerRunning) {
            long elapsedTime = SystemClock.elapsedRealtime() - startTime;
            timerRunning = false;
            startButton.setEnabled(true);
            stopButton.setEnabled(false);

            String taskName = "Task " + (tasksMap.size() + 1);
            tasksMap.put(taskName, elapsedTime);
            tasksList.add(taskName + ": " + formatTime(elapsedTime));
            tasksAdapter.notifyDataSetChanged();
        }
    }
    private void updateTimer() {
        if (timerRunning) {
            long elapsedTime = SystemClock.elapsedRealtime() - startTime;
            timerTextView.setText(formatTime(elapsedTime));
            // Schedule the next update after 1 second
            timerTextView.postDelayed(new Runnable() {
                @Override
                public void run() {
                    updateTimer();
                }
            }, 1000);
        }
    }
    private String formatTime(long timeInMillis) {
        int seconds = (int) (timeInMillis / 1000);
        int minutes = seconds / 60;
        seconds %= 60;
        return String.format("%02d:%02d", minutes, seconds);
    }
}
